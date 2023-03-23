
## Test

Launch server
``` bash
docker-compose up --build -d
```

SWAGGER
``` bash
open http://127.0.0.1:3333/api/docs/index.html
```


## kill serve

> lsof -i :3333
to check if the port is busy or not if any process is listening to the port 8099 it will be displayed with port id

> sudo  kill -9 [PID]


