This document describes how to patch `virt-handler` pods with a customized image that solves the issue: [https://github.com/harvester/harvester/issues/3586](https://github.com/harvester/harvester/issues/3586)

## Steps

1. Make sure you are running a Harvester v1.1.1 cluster.
2. SSH into a control-plane node and become root.
3. Run the following command to patch the `virt-handler` pods:

```
kubectl patch kubevirts kubevirt -n harvester-system --type=json -p='[{"op":"replace", "path":"/spec/customizeComponents/patches/2/patch", "value":"{\"spec\":{\"template\":{\"spec\":{\"containers\":[{\"name\":\"virt-controller\", \"resources\":{\"limits\":{\"cpu\":\"800m\",\"memory\":\"1300Mi\"}}, \"image\":\"registry.suse.com/harvester-beta/virt-controller:0.54.0-1\", \"imagePullPolicy\":\"Always\"}]}}}}"}]'
```

4. Verify that the `virt-controller` pods are restarted and have the patched image:

The output should be:

```
registry.suse.com/harvester-beta/virt-controller:0.54.0-1
```

## Tests

To do a quick verification if the patch work:

1. Check the `volumeDevices` on hp-volume- pod
2. Manually kill the hp-volume- pod
3. Check the `volumeDevices` again with the respawn hp-volume pod, it should exist.

If the `volumeDevices` exists, our patch should be fine. Then try to create VM and do some related tests.

## References
- https://kubevirt.io/user-guide/operations/customize_components/