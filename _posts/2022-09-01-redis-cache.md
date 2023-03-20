---
layout: post
title: redis cache 적용
categories: experience
tags: [python, redis, project]
excerpt: 데이터가 많은 API 응답을 레디스 캐시를 적용해 성능을 개선한 기록입니다.
---

## API 캐시 적용

쿼리의 데이터 양이 많아 캐시를 적용했습니다. API 요청이 올 때, 캐시에 데이터가 있으면 해당 데이터를 반환하고 없으면 쿼리를 수행 후 캐시에 저장합니다. 또한 캐시를 수동 혹은 job으로 실행할 수 있는 end point도 추가했습니다. 

### 프로젝트 캐시 구성

프로젝트 캐시 DB로 Redis를 사용합니다. Redis는 0부터 15까지 16개의 논리 db가 존재합니다. 프로젝트에서 2개의 db(db1, db3)를 사용합니다. `secret.json` 파일에 값(value)을 Redis db 숫자와 맵핑해두고 Redis 커넥션을 가져올 때, 해당 db를 가져옵니다.

```json
"REDIS": {
      "address": "127.0.0.1",
      "port": 6379,
      "password": null,
      "video_db": 3,
      "channel_db": 1
  }
```

```python
import redis


redis_config = config.get_attr("REDIS")
redis_db = redis_config.get("video_db")
rd = redis.Redis(
            host=redis_config.get("address"),
            port=redis_config.get("port"),
            db=redis_db,
            password=None,
        )

```

저장할 데이터 형태가 키-값 구조의 파이썬 딕셔너리이므로, Redis hashes 데이터 타입을 이용합니다. `HSET`, `HGET` 명령어를 이용해 저장/가져오기를 할 수 있습니다.

- [Redis HSET](https://redis.io/commands/hset/)
- [reids-py hset](https://redis-py.readthedocs.io/en/stable/commands.html#redis.commands.core.CoreCommands.hset)
- [Redis EXPIRE](https://redis.io/commands/expire/)
- [redis-py expire](https://redis-py.readthedocs.io/en/stable/commands.html#redis.commands.core.CoreCommands.expire)
- [Redis HGETALL](https://redis.io/commands/hgetall/)
- [reids-py hgetall](https://redis-py.readthedocs.io/en/stable/commands.html#redis.commands.core.CoreCommands.hgetall)

```python
import datetime


rd.hset(key, mapping={"a": a, "b": b})
rd.expire(key, datetime.timedelta(days=1))
rd.hgetall(key)

```

위의 기본 명령어를 이용해 Redis에 캐시하고, 비지니스 로직을 추가해 사용했습니다.
