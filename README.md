# [Ausonius Bibliography Generator](https://zanniadevweb.github.io/ausonius_bibliography_generator/)

**Ausonius Bibliography Generator** is built over [Vite](https://vitejs.dev/guide/), [Vuetify 3](https://vuetifyjs.com/) and [Vuejs 3](https://vuejs.org/). It was based upon [Vite Theme Free](https://store.vuetifyjs.com/products/vite-theme-free) from Vuetify official website.

## Browser Support

| Browser | Status |
| - | - |
| Chromium (Chrome, Edge) | ✅ Supported * |
| Firefox | ✅ Supported * |
| Safari 15.4+ | ✅ Supported |
| Safari 13. | ❗ Requires polyfill |
| Edge <79 | ⛔ Not supported |
| Internet Explorer | ⛔ Not supported |

## Getting Started

- Install Nodejs from the official [Nodejs page](https://nodejs.org/en/)
- Install Npm or Yarn from its official repo.
- Clone my repo by copying url in "<> code" and type in a terminal :
```sh
git clone https://github.com/zanniadevweb/pagesfolio.git
```
- Navigate inside folder by typing :
```sh
cd pagesfolio
```
- Change 'base' parameter in vite.config.js file by the name of your repo.

## Project Setup

```sh
npm install
```
OR
```sh
yarn install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```
OR
```sh
yarn dev
```

### Compile and Minify for Production

```sh
npm run build
```
OR
```sh
yarn build
```

### Automatic deployment of static files (dist folder) to your repository
- Go to 'Settings' from the menu. Click on Actions > General.
- Under Workflow permissions, authorize 'Read and write permissions'
- Autorize checkbox 'Allow GitHub Actions to create and approve pull requests'.
- Click on 'Save' button.
- Follow the usual procedure to deploy your local files to your repo.
- When your files are pushed to your repo, get back to 'Settings' menu.
- Click 'Pages', then 'Deploy from a branch'.
- Chose 'Main' as branch then Save. Wait until your site is live.
- Get back to '<> Code' menu and click on Add File > Create new file.
- Name it 'deploy.yml' in '.github/workflows' path
- Replace the content of your file by this setup :

```sh
name: Deploy
on:
  # Runs on pushes targeting the default branch
  push:
    branches: ['main']

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets the GITHUB_TOKEN permissions to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: 'pages'
  cancel-in-progress: true

jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Set up Node
        uses: actions/setup-node@v3
        with:
          node-version: 18
          cache: 'npm'

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build

      - name: Setup Pages
        uses: actions/configure-pages@v3

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          # Upload dist repository
          path: './dist'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1
```

- All previous instructions can be now ignored if updating code.
- Type usual commands any time you wish to push new code:
```sh
git add .
git commit -m "my message"
git push
```

- Wait for all the queued job to finish (Actions > All workflows)
- Still in 'Actions' menu, click on 'Deploy' submenu.
- There should a blue background text.
- It is written : 'This workflow has a worflow_dispatch event trigger'
- Click 'Run workflow' button right to this text.
- Wait until this job deployment is finished.
- Your site will really be live on now !
