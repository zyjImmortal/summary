## 问题

现在有这么一个实体叫Api，有下面的的一些属性，其中属性body_json和http_headers以字符串的形式存储到数据库中，该怎么做？

```json
{
    "name": "banner",
    "method": "POST",
    "domain": "https://xxxx.xx.xx",
    "path": "/x/x/xx/xxx",
    "remark": "",
    "env_id": 3,
    "project_id": 3,
    "body_type": "raw",
    "body_json": {"block_id": 8,"user_id": 30404350,"open_id": "xxxx","mask_id": "8odnih81","device": 3,"source":1},
    "http_headers": [
        {
            "key": "Content-Type",
            "value": "application/json;charset=UTF-8"
        }
    ],
}
```

