---
title: 【NestJS】DataStoreに登録する際のアレコレ
tags:
  - TypeScript
  - datastore
  - NestJS
  - googlecloud
private: false
updated_at: '2022-07-01T02:55:43+09:00'
id: 2abe5fc6eb615f5d5c2b
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

`RDB`にバックアップしたデータを保存していたが、アプリケーションに対するトラフィックが増えた場合に`Cloud SQL`のスペックを上げる以外に分散アーキテクチャを利用できる`Datastore`を利用することにした。

今回は`DataStore`にデータオブジェクト（`Entity`）を登録していく方法を残しておく

https://cloud.google.com/datastore/docs/concepts/entities?hl=ja

# やり方

```ts
@Injectable()
export class GcpClientService {
    private readonly auth: GoogleAuth
    private readonly storage: Storage
    private readonly basePrefix: string
    private readonly dataStore: Datastore
    private readonly cloudTasksClient: CloudTasksClient

    constructor(
        private logger: CustomLogger,
        private configService: ConfigService
    ) {
        const keyFile =
            configService.get('nodeEnv') === 'dev' ||
            configService.get('nodeEnv') === 'test'
                ? './development-service-acount.json'
                : undefined

        // https://github.com/googleapis/google-api-nodejs-client
        this.auth = new google.auth.GoogleAuth({
            keyFile,
            scopes: ['https://www.googleapis.com/auth/cloud-platform'],
        })

        // https://www.npmjs.com/package/@google-cloud/storage
        this.storage = new Storage({
            projectId: this.configService.get('gcp.projectId'),
            authClient: this.auth,
        })
        this.basePrefix =
            configService.get('nodeEnv') === 'dev' ||
            configService.get('nodeEnv') === 'test'
                ? `d-${this.configService.get('gcp.projectId')}-hoge-`
                : `${this.configService.get('gcp.projectId')}-hoge-`

        const auth = this.auth

        this.dataStore = new Datastore({
            projectId: this.configService.get('gcp.projectId'),
        })

        this.cloudTasksClient = new CloudTasksClient()
    }

    getDataStore = (): Datastore => {
        return this.dataStore
    }

    /**
     * @function DataStoreへの登録
     * @param uuid
     * @param hogeDto
     */
    async saveGcd(
        uuid: string,
        hogeDto: HogeDto,
    ): Promise<void> {
        const datastore = this.getDataStore()

        // フルパス文字列を配列で分割
        const { path } = hogeDto
        const paths = path.split('/')
        paths.unshift('/')

        // EntityのIDにUUIDを指定する
        const datastoreKey: entity.Key = datastore.key(uuid)

        // 各階層単位でパスをハッシュ化
        const basePaths = paths.map((path: string, index: number) => {
            return paths
                .slice(0, index + 1)
                .join('/')
                .slice(1)
        })
        
        // https://nodejs.org/api/crypto.html#crypto 
        const hashPaths = basePaths.map((base: string) => {
            return crypto
                .createHash('sha1')
                .update(base === '' ? '/' : base)
                .digest('hex')
        })

        hashPaths.map(async (hashPath: string, index: number) => {
            try {
                // await transaction.run()
                const isLast = paths.length - 1 === index
                const hashObject = {
                    // 終端の1個前が親になる
                    parent_hash: hashPaths[index - 1] ?? null,
                    display_name: paths[index],
                    hash: hashPath,
                    is_latest: true,
                }

                const entity = {
                    key: datastoreKey,
                    data: isLast
                        ? Object.assign(hogeDto, hashObject)
                        : hashObject,
                }

                const [data] = await datastore.runQuery(
                    await datastore
                        .createQuery(terminalUuid)
                        .filter('hash', '=', hashPath)
                        .filter('is_latest', '=', true)
                )

                // ディレクトリが初回登録なら登録
                if (!data.length && !isLast) {
                    await datastore.save(entity)
                } else if (!data.length && isLast) {
                    // データが無くてファイル登録の場合
                    await datastore.save(entity)
                } else if (data.length && isLast) {
                    data[0].is_latest = false
                    await datastore.update(data)
                    await datastore.save(entity)
                }
            } catch (e) {
                throw new InternalServerErrorException(e)
            }
        })
    }
}
```
