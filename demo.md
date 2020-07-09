## STEP 0: SETUP
### login
```
oc login --token=<token> --server=<server>
```

## STEP 1: SHOW CURRENT SOLUTION
```
oc delete deployment ghmirror-test
```
### select in-memory cache
```
oc process -f github-mirror.yml GITHUB_MIRROR_CACHE_TYPE=in-memory -o yaml > demo.yml

oc apply -f demo.yml
```
### check deployment and pods
```
oc get deployments

oc get pods
```
### show any pod
```
oc rsh <podname> bash
```
#### first time MISS
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/sretoolbox
```
#### next time HIT
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/sretoolbox
```

## STEP 2: DEMO REDIS

### select redis cache
```
oc delete deployment ghmirror-test
```
```
oc process -f github-mirror.yml GITHUB_MIRROR_CACHE_TYPE=redis -o yaml > demo.yml

oc apply -f demo.yml
```
### check deployment and pods

```
oc get deployments

oc get pods
```

### show one pod
```
oc rsh <pod-1-name> bash
```

#### HIT (already in cache)
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/sretoolbox
```
#### MISS (not yet in cache)
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/qontract-server
```

### show the other pod
```
oc rsh <pod-2-name> bash
```
#### HIT
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/sretoolbox
```
#### HIT
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/qontract-server
```

### show redeployment (if there's time)
```
oc delete deployment ghmirror-test
```

```
oc rsh <podname> bash
```

#### HIT
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/sretoolbox
```

#### HIT
```
curl -s -IXGET -H "Authorization: token $GITHUB_TOKEN" http://localhost:8080/repos/app-sre/qontract-reconcile
```

### OTHER USEFUL STUFF
#### show logs
```
oc logs -f <podname> | head -n 15
```