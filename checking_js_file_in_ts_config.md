# in tsconfig.json

{
  "extends": "@adonisjs/tsconfig/tsconfig.client.json",
  "compilerOptions": {
    "baseUrl": ".",
    "module": "ESNext",
    "jsx": "react-jsx",
    "paths": {
      "~/*": ["./*"],
      "@/*": ["./*"],
    },
    "allowJs": true,
    "checkJs": true,
    "noImplicitAny": false,
    "outDir": "./dist",
    "noEmit": true,
    "esModuleInterop": true
  },
  "include": ["./**/*.ts", "./**/*.tsx", "./**/*.jsx", "./**/*.js"]
}


# in .vscode/settings.json

{
  "javascript.validate.enable": true,
  "js/ts.implicitProjectConfig.checkJs": true
}
