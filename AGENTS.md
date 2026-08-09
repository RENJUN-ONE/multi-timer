# 计时器项目 — 项目规则

## 硬性要求

**本项目的所有修改,完成后都必须部署到 Netlify 并给用户可访问的链接。** 这是用户的明确要求,不得省略或只停留在本地。

## 部署目标

- 站点名: `stupendous-fenglisu-14cea9`
- 站点 ID: `1b85dd9d-4bbf-4da6-b449-9f31048c83bc`
- 访问链接: https://stupendous-fenglisu-14cea9.netlify.app/
- 部署方式: Netlify API(需要用户提供 Netlify 个人访问令牌,格式 `nfp_...`)

## 重要背景

- 线上运行的是 **GitHub 仓库版**(列表式行布局),来源: `https://github.com/RENJUN-ONE/multi-timer`
- 本地工作副本: `.deploy-multi-timer/`(该目录是仓库 clone,改动和推送都在这里进行)
- 本地根目录的 `index.html` 是**旧版设计(卡片式双栏),不要误用它去部署**
- Netlify 站点**未关联 GitHub 仓库**,推送 GitHub 不会自动更新 Netlify(仓库只启用了 GitHub Pages)
- GitHub Pages 备用链接: https://renjun-one.github.io/multi-timer/

## Netlify API 部署步骤(已验证可行)

1. 计算文件 SHA1(不是 SHA256): `Get-FileHash -Algorithm SHA1`
2. 创建部署,文件映射键**不带前导斜杠**(如 `index.html`):
   `POST https://api.netlify.com/api/v1/sites/{site_id}/deploys` ,body: `{"files": {"index.html": "<sha1>"}}`
   - 请求体必须写入临时文件后用 `--data-binary "@file"` 发送,不能直接在 PowerShell 命令行内联 JSON(引号会被破坏导致 400)
3. 上传文件内容:
   `PUT https://api.netlify.com/api/v1/deploys/{deploy_id}/files/index.html?size=<字节数>`
   - 带 `Authorization: Bearer <token>` 和 `Content-Type: application/octet-stream`
4. 轮询部署状态到 `ready`(`GET /api/v1/deploys/{deploy_id}`)
5. 验证线上页面已更新(抓取 https://stupendous-fenglisu-14cea9.netlify.app/ 检查新版标记,如 `deadline`)

失败排查要点: 422 "no records matched" = 摘要算法或文件路径格式错误(SHA1 + 无前导斜杠); 卡在 uploading 的部署可 POST `/deploys/{id}/cancel` 取消。

## 令牌

- 不要在此文件或代码中保存任何令牌明文。
- 每次部署需要向用户索要新的 Netlify 令牌,并在完成后提醒用户吊销。
