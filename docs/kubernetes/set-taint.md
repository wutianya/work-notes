# TAINT

## Add a taint

```kubectl taint nodes node dedicated=foo:NoSchedule```

## Remove taint

```kubectl taint nodes node dedicated:NoSchedule-```

## Reference

> spec.template.spec.tolerations

```kubectl
tolerations:
- effect: NoSchedule
  key: dedicated
  operator: Exists
```