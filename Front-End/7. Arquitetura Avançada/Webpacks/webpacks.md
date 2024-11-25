# 📚 Guia Detalhado: Ferramentas de Build

## 1. Bundlers (Empacotadores)
São ferramentas que transformam seu código fonte em arquivos otimizados para produção.

### 1.1 Webpack
O Webpack é um bundler que pega seu código JavaScript, CSS, imagens e outros recursos e os combina em pacotes otimizados.

```javascript
// webpack.config.js explicado em detalhes
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  // Define onde começa a aplicação
  entry: './src/index.js',
  
  // Define onde e como os arquivos serão gerados
  output: {
    path: path.resolve(__dirname, 'dist'), // Pasta de saída
    filename: '[name].[contenthash].js',   // Nome do arquivo com hash para cache
    clean: true                            // Limpa a pasta dist antes de cada build
  },
  
  // Define como diferentes tipos de arquivos são processados
  module: {
    rules: [
      // Regra para arquivos JS/TS
      {
        test: /\.(js|ts)x?$/,              // Processa arquivos .js, .ts, .jsx, .tsx
        exclude: /node_modules/,            // Ignora node_modules
        use: {
          loader: 'babel-loader',           // Usa Babel para transpilar
          options: {
            presets: [
              '@babel/preset-env',          // Para JS moderno
              '@babel/preset-react',        // Para React
              '@babel/preset-typescript'    // Para TypeScript
            ]
          }
        }
      },
      
      // Regra para arquivos CSS
      {
        test: /\.css$/,                    // Processa arquivos .css
        use: [
          MiniCssExtractPlugin.loader,     // Extrai CSS em arquivos separados
          'css-loader',                    // Processa imports e url()
          'postcss-loader'                 // Processa PostCSS
        ]
      },
      
      // Regra para imagens
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i, // Processa imagens
        type: 'asset/resource'              // Gera arquivo separado
      }
    ]
  },
  
  // Plugins adicionam funcionalidades extras
  plugins: [
    // Gera o HTML final com os bundles injetados
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    // Extrai CSS em arquivos separados
    new MiniCssExtractPlugin({
      filename: 'styles.[contenthash].css'
    })
  ],
  
  // Configurações de otimização
  optimization: {
    splitChunks: {
      chunks: 'all',                // Divide o código em chunks
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/, // Separa dependências em arquivo vendor
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
  }
};
```

### 1.2 Rollup
Rollup é um bundler mais simples, focado em ES modules e tree-shaking eficiente.

```javascript
// rollup.config.js explicado
export default {
  input: 'src/index.ts',           // Arquivo de entrada
  output: [
    {
      file: 'dist/bundle.cjs.js',  // Saída CommonJS
      format: 'cjs'
    },
    {
      file: 'dist/bundle.esm.js',  // Saída ES Module
      format: 'es'
    }
  ],
  plugins: [
    resolve(),                     // Resolve imports de node_modules
    commonjs(),                    // Converte CommonJS para ES Modules
    typescript(),                  // Processa TypeScript
    terser()                       // Minifica o código
  ],
  external: ['react', 'react-dom'] // Não inclui estas dependências no bundle
};
```

## 2. Transpiladores
Convertem código moderno para versões mais antigas compatíveis com navegadores antigos.

### 2.1 Babel
```javascript
// babel.config.js explicado
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: {
        browsers: [
          '> 1%',                 // Navegadores com mais de 1% de uso
          'last 2 versions',      // Últimas 2 versões de cada navegador
          'not dead'              // Navegadores ainda mantidos
        ]
      },
      useBuiltIns: 'usage',       // Inclui polyfills automaticamente
      corejs: 3                   // Versão do core-js para polyfills
    }],
    '@babel/preset-react',        // Suporte a JSX
    '@babel/preset-typescript'    // Suporte a TypeScript
  ],
  plugins: [
    // Plugins adicionais
    ['@babel/plugin-transform-runtime', {
      regenerator: true           // Suporte a generators/async
    }],
    '@babel/plugin-proposal-class-properties' // Suporte a propriedades de classe
  ]
};
```

## 3. Técnicas de Otimização

### 3.1 Code Splitting
Divide o código em partes menores carregadas sob demanda.

```javascript
// Exemplo de code splitting em React
const MyComponent = React.lazy(() => import('./MyComponent'));
// O componente só será carregado quando necessário

// Configuração no webpack
optimization: {
  splitChunks: {
    chunks: 'all',                // Aplica splitting em todo código
    minSize: 20000,              // Tamanho mínimo para criar chunk
    minChunks: 1,                // Uso mínimo para criar chunk
    maxAsyncRequests: 30,        // Máximo de requisições paralelas
    cacheGroups: {
      // Agrupa código de node_modules
      defaultVendors: {
        test: /[\\/]node_modules[\\/]/,
        priority: -10
      }
    }
  }
}
```

### 3.2 Tree Shaking
Remove código não utilizado do bundle final.

```javascript
// Exemplo de código que permite tree shaking
// Apenas a função usada será incluída no bundle final
export const sum = (a, b) => a + b;        // Será incluída
export const subtract = (a, b) => a - b;    // Será removida

import { sum } from './math';              // Importa apenas sum
```

### 3.3 Minificação
Reduz o tamanho do código removendo espaços, comentários e encurtando nomes.

```javascript
// Configuração de minificação no webpack
optimization: {
  minimize: true,
  minimizer: [
    new TerserPlugin({
      terserOptions: {
        compress: {
          drop_console: true,     // Remove console.log
          dead_code: true         // Remove código morto
        },
        output: {
          comments: false         // Remove comentários
        }
      }
    })
  ]
}
```

## 4. Scripts NPM Comuns
```json
{
  "scripts": {
    "dev": "webpack serve --mode development",     // Ambiente de desenvolvimento
    "build": "webpack --mode production",          // Build de produção
    "build:analyze": "webpack --mode production --analyze", // Analisa tamanho
    "typecheck": "tsc --noEmit",                  // Verifica tipos TS
    "lint": "eslint src --ext .ts,.tsx",          // Verifica código
    "test": "jest",                               // Roda testes
    "prepare": "husky install"                    // Configura git hooks
  }
}
```

## 5. Dicas Importantes

1. **Cache**
   - Use hashes nos nomes dos arquivos para cache-busting
   - Configure corretamente o cache no servidor

2. **Monitoramento**
   - Use o Bundle Analyzer para verificar tamanhos
   - Monitore o tempo de carregamento
   - Implemente métricas de performance

3. **Desenvolvimento**
   - Use Hot Module Replacement (HMR)
   - Configure source maps adequadamente
   - Mantenha dependências atualizadas
