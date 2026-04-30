To quickly explore how to build applications with features like short dramas and interactive videos, you can use this guide to set up and run the VideoOne Web demo. This sample project demonstrates the core capabilities of the VideoOne SDK, helping you understand its integration and accelerate your own app development.
# Prerequisites

* An operating system with a graphical user interface
* A terminal or command line interface
* A web browser compatible with the Web SDK. We recommend using the latest version of Chrome for the optimal experience.
* The [pnpm](https://pnpm.io/installation) package manager is installed.

# Procedures
## Step 1: Clone the project and install dependencies

1. Run `git clone https://github.com/byteplus-sdk/videoone-example.git` to clone the [videoone-example](https://github.com/byteplus-sdk/videoone-example) repository.
2. In the project root directory, run `pnpm install --registry=https://registry.npmmirror.com` to install dependencies.

## Step 2: Start the project

1. Run `pnpm run dev` to start the project. The default port number is 8000.
2. Open [localhost:8000/videoone](localhost:8000/videoone) in your browser to view the demo project. When you open the URL, the homepage shows two scenarios: "Short drama" and "Interactive video".
   1. Click on "Short drama" to explore continuous, vertical full-screen episodes with swipe-to-switch and content unlocking features.
   2. Click on "Interactive video" to see a dynamic video implementation with scrollable content, commenting, and liking features.
      
   | Homepage | Short drama | Interactive video |
   | --- | --- | --- |
   | ![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/ce65fcea68b845089d4c8d253eb8f7c9~tplv-goo7wpa0wc-image.image) <br>  | ![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/1a27e4f281e64def93a1aafa2bca4109~tplv-goo7wpa0wc-image.image) <br>  | ![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/cfe034e2fe8345298af91076f2320427~tplv-goo7wpa0wc-image.image) <br>  |

## Step 3: Build and deploy
To generate the build output for deployment, run the following command: `pnpm run build`.
For deployment, pay special attention to the `base` and `build` configurations in `vite.config.ts`. Consider two key aspects:

1. **Build output directory:** By default, the build output is generated into the `output` directory. This can be customized by modifying the `outDir` property under the `build` configuration.
2. **Public path prefix for build resources:** This setting determines the public base path for all assets during the build process.
   * If your built resources are hosted directly by the server at the root or a fixed sub-path, the `base` can typically be set to `/` (root path) or an empty string.
   * If static resources are uploaded to a Content Delivery Network (CDN) for hosting, you will need to obtain the corresponding CDN acceleration domain name. This value should then replace `*` in the `base` configuration.
      ```TypeScript
      export default defineConfig({
        base: isProd ? '*' : '/',
        server: {
          port: 8000,
        },
        build: {
          outDir: 'output',
        },
      });
      ```


# Understanding the code
The directory tree of the demo project is as follows:
```Plain Text
.
├── build.sh
├── commitlint.config.cjs
├── index.html            // Template entrance
├── node_modules
├── package.json          // Project manifest and dependencies
├── pnpm-lock.yaml
├── public // Common files
├── README.md
├── src
│   ├── @types           // TypeScript type declarations
│   ├── App.tsx
│   ├── assets           // Static resources, such as images, SVGs, Lottie animations, etc.
│   ├── components       // Components
│   ├── hooks
│   ├── index.less
│   ├── interface        // Interface declarations
│   ├── main.tsx
│   ├── pages            // Page routing
│   ├── player.ts
│   ├── plugins
│   ├── redux
│   ├── service          // API service definitions
│   ├── utils
│   └── vite-env.d.ts
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts        // Vite configuration
```


