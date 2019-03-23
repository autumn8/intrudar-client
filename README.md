# Intruder Detection Client

Client side human detection with tensor flow.

Connects to local socket.io server streaming image stills from raspberry pi camera, and feeds images to tensor. This is as an early implementation and is super heavy handed.

Next step: 
1) Implement simple image diff to detect some motion before passing through tensor flow.
2) Hook up PIR motion sensor and run tensor on motion trigger.



## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Lints and fixes files
```
npm run lint
```
