
> **Warning**
> The doc is for testing a custom-build image in a specific cluster, please do not follow it without any context.

`Kubevirt` is one of the upstreams of Harvester, sometimes we need to add new features or fix bug to it.

# 1. virt-controller, virt-api, virt-handler, virt-luancher

This document describes how to patch `virt-controller` (as an example) pods with a customized image that solves the issue: [https://github.com/harvester/harvester/issues/3586](https://github.com/harvester/harvester/issues/3586)

For `virt-luncher`, `virt-api`, `virt-handler`, the process is similar. For `virt-operator`, read chapter 2.

## Steps

1. Make sure you are running a Harvester v1.1.1 cluster.
2. SSH into a control-plane node and become root.
3. Add following to `.spec.diff.comparePatches` of managedchart `harvester`
```
kubectl edit managedchart -n fleet-local harvester
    - apiVersion: kubevirt.io/v1
      jsonPointers:
      - /spec/customizeComponents/patches
      kind: KubeVirt
      name: kubevirt
```

Without it, the managedchart `harvester` will be complained by `fleet-agent`.

4. Run the following command to patch the `virt-controller` pods:

    
    kubectl patch kubevirts kubevirt -n harvester-system --type=json -p='[{"op":"add", "path":"/spec/customizeComponents/patches/-", "value": {"patch":"{\"spec\":{\"template\":{\"spec\":{\"containers\":[{\"name\":\"virt-controller\", \"image\":\"registry.suse.com/harvester-beta/virt-controller:0.54.0-1\",\"imagePullPolicy\":\"Always\"}]}}}}", "resourceName": virt-controller, "resourceType": Deployment, "type": strategic}}]'
    



5. Verify that the `virt-controller` pods are restarted and have the patched image:
    ```
    kubectl get deployment virt-controller -n harvester-system -o=jsonpath='{$.spec.template.spec.containers[0].image}'
    ```

    The output should be:

    ```
    registry.suse.com/harvester-beta/virt-controller:0.54.0-1
    ```

## Tests

To do a quick verification if the patch work:
1. Create a VM and hotplug a volume to it.
2. Run the following command to get the VM and its hotplug volume pod. **NOTE**: `default` is the namespace of VM.
    ```
    kubectl get pods -n default
    ```
3. Choose one hp-volume- pod. For example, choose `hp-volume-5kwps`.
    ![截圖 2023-03-21 上午9 22 49](https://user-images.githubusercontent.com/1615081/226499717-0f2bcc63-7155-4aea-953e-74368e9be092.png)

4. Get the `volumeDevices` on the above pod.
    ```
    # kubectl get pods hp-volume-5kwps -o yaml | yq -e '.spec.containers[0].volumeDevices'
    - devicePath: /path/vm001-vol001/5ab5bb06-05b3-4faf-a7c5-50f9b45a79aa
      name: vm001-vol001
    ```
5. Manually kill the hp-volume- pod, for example, `hp-volume-5kwps`
    ```
    # kubectl delete pods hp-volume-5kwps -n default
    pod "hp-volume-5kwps" deleted
    ```
6. Then find the respawn hp-volume- pod.
    ![截圖 2023-03-21 上午9 43 06](https://user-images.githubusercontent.com/1615081/226500494-3b72d96f-5046-42a5-8648-b8c577558943.png)

7. `hp-volume-zqrdf` is the respawn hp-volume- pod, check `volumeDevices` again. It should be the same as above.
    ```
    # kubectl get pods hp-volume-zqrdf -o yaml | yq -e '.spec.containers[0].volumeDevices'
    - devicePath: /path/vm001-vol001/5ab5bb06-05b3-4faf-a7c5-50f9b45a79aa
      name: vm001-vol001
    ```

If the `volumeDevices` exists and is the same as the above, our patch should be fine. Then try to create VMs and do some related tests.

## References
- https://kubevirt.io/user-guide/operations/customize_components/

# 2. virt-operator

The process is a bit different with above, it will be as such:

1. Build kube-virt and push the image set (virt-operator,virt-controller, virt-api...) to an repository.
2. Add following to `.spec.diff.comparePatches` of managedchart `harvester`.
```
    - apiVersion: apps/v1
      jsonPointers:
      - /spec/template
      kind: Deployment
      name: virt-operator
```
3. Edit deployment `virt-operator`, replace following with your image&tag.
```
kubectl edit deployment -n harvester-system virt-operator

          value: registry.suse.com/suse/sles/15.4/virt-operator:0.54.0-150400.3.16.1
        image: registry.suse.com/suse/sles/15.4/virt-operator:0.54.0-150400.3.16.1
```
4. Note: the `virt-operator` will replace all images of `kubevirt` to this repository&tag.
 check the image of the deployment/pod of kubevirt
5. Check the log of `virt-operator`,if below messages are there, it means the `virt-operator` runs sccessfully, when failure, the log also tells where and why.

> {"component":"virt-operator","kind":"KubeVirt","level":"info","msg":"All KubeVirt resources created","name":"kubevirt","namespace":"harvester-system","pos":"kubevirt.go:1060","timestamp":"2023-05-24T12:18:37.836326Z","uid":"65259313-68e5-413e-9e7a-763839d21b2c"}

> {"component":"virt-operator","kind":"KubeVirt","level":"info","msg":"All KubeVirt components ready","name":"kubevirt","namespace":"harvester-system","pos":"kubevirt.go:1064","timestamp":"2023-05-24T12:18:37.836419Z","uid":"65259313-68e5-413e-9e7a-763839d21b2c"}

## References
- https://github.com/harvester/harvester/issues/3792
- https://github.com/kubevirt/kubevirt/pull/9846