# 🛠️ DevOps Templates

這是個人的共用 CI/CD 流程庫 (Reusable Workflows)。
旨在標準化所有 .NET 專案的建置、測試、發佈與文件流程。

## 📦 Available Workflows

| 功能 | 檔案路徑 | 用途 |
| :--- | :--- | :--- |
| **NuGet Publish** | `reusable-nuget-publish.yml` | 自動建置、測試並發佈 NuGet 套件 |
| **DocFX Site** | `reusable-docfx-publish.yml` | 自動建置 DocFX 文件並部署至 GitHub Pages |

---

## 🚀 1. NuGet Publish

負責執行：`Restore` -> `Build` -> `Test (若有)` -> `Pack` -> `Push to NuGet`。
**注意：** 建議在 Caller Workflow 指定 `runs-on: windows-latest` 以支援完整 Framework (如 .NET 4.5)。

### 前置需求 (必看)

1. **API Key**：需至 Repo 的 **Settings** > **Secrets and variables** > **Actions**，新增一個 Repository Secret，名稱為 `NUGET_API_KEY`，並填入您的 NuGet API Key。

### 引用範例

```yaml
jobs:
  publish:
    uses: CloudyWing/devops-templates/.github/workflows/reusable-nuget-publish.yml@main
    with:
      dotnet-version: '10.0.x' # 預設 10.0.x
    secrets:
      NUGET_API_KEY: ${{ secrets.NUGET_API_KEY }}

```

### Inputs & Secrets

| 參數 | 必填 | 預設值 | 說明 |
| --- | --- | --- | --- |
| `dotnet-version` | ❌ | `10.0.x` | 指定 SDK 版本 |
| `NUGET_API_KEY` | ✅ | - | (Secret) NuGet API Key，必須透過 secrets 傳入 |

---

## 📚 2. DocFX Site

自動編譯專案內的 XML 註解與 Markdown 文件，並發佈至 GitHub Pages。

### 前置需求 (必看)

1. **設定檔**：呼叫端的專案根目錄需有 `docfx.json`。
2. **權限設定**：需至 Repo 的 **Settings** > **Pages**，將 Source 設定為 **Deploy from a branch**，並選擇 `gh-pages` 分支 (第一次執行 Action 成功後才會出現該分支)。

### 引用範例

```yaml
jobs:
  deploy-docs:
    uses: CloudyWing/devops-templates/.github/workflows/reusable-docfx-publish.yml@main
    with:
      docfx-config: 'docfx.json' # 若您的設定檔在子目錄，請修改此處
      dotnet-version: '10.0.x'
    # 不需要傳遞 Secret，此 Workflow 會自動使用 GITHUB_TOKEN 推送

```

### Inputs

| 參數 | 必填 | 預設值 | 說明 |
| --- | --- | --- | --- |
| `docfx-config` | ❌ | `docfx.json` | 指定 docfx.json 的路徑 |
| `dotnet-version` | ❌ | `10.0.x` | 指定 SDK 版本 |

## License

This project is MIT [licensed](./LICENSE.md).
