# kubectl-ps

`kubectl-ps` is a lightweight **kubectl plugin** that shows processes-style
tables (ala `ps aux`) for Pods, Nodes and Namespaces with fully-customisable
resource columns.

- **Zero server-side components.**  
  Reads live data via the Kubernetes API and, when available, the
  `metrics-server`.
- **Flexible column set.**  
  Mix any combination of *memory* and *CPU* metrics, choose request/limit/usage,
  add percentages or free/available columns.
- **Human-readable or raw units.**  
  Switch between Gi/Mi, raw bytes or terse “3.2G / 850M”.
- **Totals & sorting.**  
  Sort by any metric (descending by default) and append an aggregated `TOTAL`
  row.
- **Single static binary.**  
  Build once, drop into `$PATH`, done.

---

### Install

```bash
go install github.com/aenix-io/kubectl-ps@latest
```

### Quick start

```bash
# CPU usage vs requests for pods, include node column
kubectl ps pods curn

# Memory requests / limits / percent for all namespaces
kubectl ps ns mrlp -A

# Show allocatable, available and percent on nodes
kubectl ps nodes cmafprl -t
```

### Usage:

```bash
Usage:
    kubectl ps <pods|nodes|namespaces> <flags> [options]

Scopes:
    pods | nodes | namespaces

Metric flags:
    m  memory      u  usage
    c  cpu         r  requests
    p  percent     l  limits
                   n  node  (pods only)
                   f  free  (nodes only)
                   t  total (nodes only)

Options:
    -A                all namespaces / all nodes
    -n <namespace>    select namespace
    -r                reverse sort
    -h                human-readable units
    -m                mebibytes
    -g                gibibytes
    -b                bytes
    -t                show TOTAL
```


**Output rules**

- **Columns are sorted by the primary metric** (the first metric letter on the first family letter).
- **% always shows `second % first`** of the two numeric columns printed
immediately before it; if it appears first, the command falls back to the
first two numeric columns of that family.
- **Use -t** to show total row with aggregated values for all rows.


## Examples

Memory and CPU usage with requests for pods, including total row, sorted by memory usage:

```console
$ kubectl ps pod mcurp -n kube-system -t
NAME                                   STATUS   MEM_USE  MEM_REQ  MEM_USE_REQ  CPU_USE  CPU_REQ  CPU_USE_REQ  AGE
kube-apiserver-talos-o10-doj           Running  3.59G    512.0M   718%         545      200      272%         28h
kube-apiserver-talos-xgn-lip           Running  2.62G    512.0M   524%         278      200      139%         28h
kube-apiserver-talos-qec-cr2           Running  2.08G    512.0M   416%         396      200      198%         16h
kube-controller-manager-talos-o10-doj  Running  267.0M   256.0M   104%         27       50       54%          28h
kube-scheduler-talos-o10-doj           Running  82.1M    64.0M    128%         5        10       50%          19h
kube-scheduler-talos-qec-cr2           Running  72.8M    64.0M    114%         5        10       50%          16h
kube-scheduler-talos-xgn-lip           Running  68.6M    64.0M    107%         4        10       40%          28h
kube-controller-manager-talos-qec-cr2  Running  37.8M    256.0M   15%          2        50       4%           16h
kube-controller-manager-talos-xgn-lip  Running  32.4M    256.0M   13%          2        50       4%           28h
coredns-cc8bf9fd8-zrf5b                Running  30.8M    70.0M    44%          17       100      17%          28h
coredns-cc8bf9fd8-px9dp                Running  26.1M    70.0M    37%          17       100      17%          27h
TOTAL                                  -        8.90G    2.57G    346%         1298     980      132%         -
```

CPU usage vs requests and limits for namespaces, sorted by CPU usage:

```console
$ kubectl ps ns curl -r
NAME                            STATUS  CPU_USE  CPU_REQ  CPU_LIM  AGE
tenant-prod                     Active  3123     8431     71825    233d
tenant-root                     Active  813      3984     23927    234d
kube-system                     Active  669      980      -        234d
cozy-system                     Active  478      -        -        234d
cozy-kubeovn                    Active  429      420      22000    234d
cozy-monitoring                 Active  359      160      200      234d
cozy-cilium                     Active  99       -        -        234d
cozy-linstor                    Active  76       -        -        234d
cozy-metallb                    Active  49       -        -        234d
cozy-dashboard                  Active  44       420      -        234d
```

Memory free vs total on nodes and percentage:

```console
$ kubectl ps nodes mrtp
NAME           STATUS  MEM_TOTAL  MEM_REQ  MEM_TOTAL_REQ  AGE
talos-o10-doj  Ready   30.52G     32.23G   95%            197d
talos-xgn-lip  Ready   30.52G     29.49G   103%           197d
talos-qec-cr2  Ready   30.52G     28.28G   108%           234d
```

## License

Apache-2.0, see [LICENSE](LICENSE).

