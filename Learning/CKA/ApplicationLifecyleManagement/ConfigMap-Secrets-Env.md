```
env:
- name: APP_COLOR
  value: Pink
```

```
env:
- name: APP_COLOR
  valueFrom:
	  configMapKeyRef:
```

```
env:
- name: APP_COLOR
  valueFrom:
	  secretKeyRef:
```