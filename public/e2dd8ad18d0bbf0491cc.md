---
title: 【NestJS】GCSからSigned URLをクライアントサイドに返却するアレコレ
tags:
  - GCS
  - NestJS
  - GoogleCloud
private: false
updated_at: '2022-07-01T03:06:16+09:00'
id: e2dd8ad18d0bbf0491cc
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

`Cloud Storage`に保存している画像の`Signed URL`をクライアントサイドに返却するまでのプロセスをまとめる

https://cloud.google.com/storage/docs/access-control/signed-urls

# やり方

```ts
@Injectable()
export class GcpClientService {
    private readonly storage: Storage
    private readonly auth: GoogleAuth

    constructor(
        private configService: ConfigService
    ) {
        // https://www.npmjs.com/package/@google-cloud/storage
        this.storage = new Storage({
            projectId: this.configService.get('gcp.projectId'),
            authClient: this.auth,
        })
    }

    async downloadFiles(id: string, file_tree_id: number) {
        const fileMap = await this.backupFileMapRepository.findOne({
            where: {id: file_tree_id, is_latest: true},
        })

        if (!fileMap) {
            throw new NotFoundException('ファイルツリーが見つかりませんでした')
        }

        const storage = this.gcpClientService.getStorage()

        return await storage
            .bucket(contractor.gcp_storage_bucket_name)
            .file(`${id}/blob/${fileMap.file_hash}`)
            .getSignedUrl({
                action: 'read',
                expires: Date.now() + 15 * 60 * 1000,
                queryParams: {
                    'response-content-disposition': `attachment; filename=${path.basename(
                        fileMap.path
                    )}`,
                },
            })
    }
}
```
