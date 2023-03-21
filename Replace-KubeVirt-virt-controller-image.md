This document describes how to patch `virt-controller` pods with a customized image that solves the issue: [https://github.com/harvester/harvester/issues/3586](https://github.com/harvester/harvester/issues/3586)

## Steps

1. Make sure you are running a Harvester v1.1.1 cluster.
2. SSH into a control-plane node and become root.
3. Run the following command to patch the `virt-controller` pods:

    ```
    kubectl patch kubevirts kubevirt -n harvester-system --type=json -p='[{"op":"add", "path":"/spec/customizeComponents/patches/-", "value": {"patch":"{\"spec\":{\"template\":{\"spec\":{\"containers\":[{\"name\":\"virt-controller\", \"image\":\"registry.suse.com/harvester-beta/virt-controller:0.54.0-1\",\"imagePullPolicy\":\"Always\"}]}}}}", "resourceName": virt-controller, "resourceType": Deployment, "type": strategic}}]'
    ```

4. Verify that the `virt-controller` pods are restarted and have the patched image:
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
1. Run the following command to get the VM and its hotplug volume pod. **NOTE**: `default` is the namespace of VM.
    ```
    kubectl get pods -n default
    ```
1. Choose one hp-volume- pod. For example, choose `hp-volume-5kwps`.
    ![截圖 2023-03-21 上午9 22 49](https://user-images.githubusercontent.com/1615081/226499717-0f2bcc63-7155-4aea-953e-74368e9be092.png)

1. Get the `volumeDevices` on the above pod.
    ```
    # kubectl get pods hp-volume-5kwps -o yaml | yq -e `.spec.containers[0].volumeDevices`
    - devicePath: /path/vm001-vol001/5ab5bb06-05b3-4faf-a7c5-50f9b45a79aa
      name: vm001-vol001
    ```
1. Manually kill the hp-volume- pod, for example, `hp-volume-5kwps`
    ```
    # kubectl delete pods hp-volume-5kwps -n default
    pod "hp-volume-5kwps" deleted
    ```
1. Then find the respawn hp-volume- pod.
    ![截圖 2023-03-21 上午9 43 06](https://user-images.githubusercontent.com/1615081/226500494-3b72d96f-5046-42a5-8648-b8c577558943.png)

1. `hp-volume-zqrdf` is the respawn hp-volume- pod, check `volumeDevices` again. It should be the same as above.
    ```
    # kubectl get pods hp-volume-zqrdf -o yaml | yq -e `.spec.containers[0].volumeDevices`
    - devicePath: /path/vm001-vol001/5ab5bb06-05b3-4faf-a7c5-50f9b45a79aa
      name: vm001-vol001
    ```

If the `volumeDevices` exists and is the same as the above, our patch should be fine. Then try to create VMs and do some related tests.

## References
- https://kubevirt.io/user-guide/operations/customize_components/