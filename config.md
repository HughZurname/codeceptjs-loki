**Add to `codecept.conf.js` with:**

```javascript
exports.config = {
    helpers: {
        Nightmare: {
            url: "http://localhost"
            },
        Loki: {
            "require": "node_modules/codeceptjs-loki",
            "dbName": "db.json",
            "dbSeed": true
        }
    }
    /*...some config*/
}
```

**or to `codecept.json` with:**

```json
{
  "helpers": {
    "Nightmare": {
      "url": "http://localhost"
    },
    "Loki": {
      "require": "node_modules/codeceptjs-loki",
      "dbName": "db.json",
      "dbSeed": true
    }
  }
}
```