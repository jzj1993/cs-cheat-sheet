# node-cross-env



package.json

```json
{
    "scripts": {
        "start": "cross-env NODE_ENV=production node $NODE_DEBUG_OPTION src/index.js",
        "dev": "cross-env NODE_ENV=development node $NODE_DEBUG_OPTION src/index.js",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "dependencies": {
        "cross-env": "^5.2.0"
    }
}
```



read environment in code

```js
// config.js
const production = {
    serverPort:9000
}

const development = {
    ...production,
    serverPort:8000
}

module.exports = (process.env.NODE_ENV === 'development') ? development : production
```

