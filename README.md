# css-build-script
css-build-script for scss less

```
// name: build.mjs

// Collect all style files and output them into one file (such as './index.scss')

import colors from 'colors-console'
import { appendFileSync, readdir, stat, unlink, existsSync } from 'fs'
import { dirname, extname, join, resolve } from 'path'
import { fileURLToPath } from 'url'
import { unlinkSync } from "node:fs";

const __filename = fileURLToPath(import.meta.url)
const __dirname = dirname(__filename)

function error(label, err) {
  return console.error(colors(['red'], label), err)
}

const matchExtNames = ['.less']
const targetPath = resolve(__dirname)
const outputPath = resolve(__dirname, './index.less')

function writeFileFunc(filePath) {
  readdir(filePath, function (err, files) {
    if (err) {
      error('Error: \n %s', err)
    } else {
      files.forEach((fileName) => {
        const fullFilePath = join(filePath, fileName)
        stat(fullFilePath, function (err, status) {
          if (err) {
            error('Error: \n %s', err)
          } else {
            const _extname = extname(fullFilePath)
            if (status.isFile() && matchExtNames.includes(_extname)) {
              try {
                appendFileSync(outputPath, `@import "${ fullFilePath.replace(`${ __dirname }/`, '') }";\n`)
              } catch (e) {
                error('Error: \n %s', err)
              }
            }
            if (status.isDirectory()) writeFileFunc(fullFilePath)
          }
        })
      })
    }
  })
}

function main() {
  try {
    if (existsSync(outputPath)) unlinkSync(outputPath)
    writeFileFunc(targetPath)
    console.info(colors(['green'], 'OK'))
  } catch (err) {
    error('Error: \n %s', err)
  }
}

main()

```
