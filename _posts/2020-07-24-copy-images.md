---
layout: post
title: Copy image
category: Code
published: false
tags: NodeJs Images Copy
---

# Copy images without losing datestamp

The following script generates a batfile with robocopy. The destination folder will be /YYYY-MM/filename.xxx.

```javascript
var fs = require('fs');

let pathSource = "C:\\Users\\Desktop\\folder1\\"
let pathDestination = "C:\\Users\\Desktop\\folder2\\"

async function main() {
    let files = await getFiles(pathSource);
    let bat = "";
    for (const file of files) {
        let filenameFrom = `${pathSource}${file}`;
        let stats = await getStat(filenameFrom);
        let birthtime = stats.birthtime;
        let ctime = stats.ctime;
        let outputFolder = `${pathDestination}${birthtime.getFullYear()}-${('0' + (birthtime.getMonth() + 1)).slice(-2)}`;

        if (!fs.existsSync(outputFolder)) {
            fs.mkdirSync(outputFolder);
        }

        bat += `robocopy ${pathSource} ${outputFolder} ${file}\r\n`;
    }

    bat += `pause`;
    fs.writeFile(`${pathSource}\copy.bat`, bat, function (err) {
        if (err) return console.log(err);
        console.log('Bat-file saved!');
    });
}

function getFiles(path) {
    return new Promise((resolve, reject) => {
        fs.readdir(path, (err, files) => {
            if (err) reject(err);
            resolve(files);
        });
    });
}

function getStat(filename) {
    return new Promise((resolve, reject) => {
        fs.stat(filename, (err, stats) => {
            if (err) reject(err);
            resolve(stats);
        });
    });
}

main();
```
