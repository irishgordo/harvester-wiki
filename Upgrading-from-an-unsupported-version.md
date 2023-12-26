We have a [minimum upgradable version restriction](https://github.com/harvester/harvester/issues/2431) set for each Harvester release. That ensures our support effort can focus only on specific upgrade paths. For example, [the minimum upgradable version of Harvester v1.2.1 release is v1.1.2.](https://github.com/harvester/harvester/blob/9c9e09609559eaaf867fdf881c8d0cb490442b34/package/upgrade-matrix.yaml#L3) That means the user cannot perform an upgrade assuming they have a running v1.1.1 cluster.

Sometimes, the Devs/QAs might want to validate the upgrade behavior from an unsupported version, such as a cluster installed with a dev build, sprint release, or release candidate ISO image. The minimum upgradable version restriction will be their nightmare. Fortunately, we already have a way to work around such restriction. Here's how:

> Please note that upgrading from **unsupported versions** of Harvester is **not recommended** in any way. Only perform this action if necessary.

Say if you want to upgrade from a dev build `v1.1-head` to `v1.2.1`, normally you'll be blocked with the message like the following:

![image (1)](https://github.com/harvester/harvester/assets/1827717/2d86f3b7-108f-4a4e-9ee8-52afa68a1ab2)

1. Paused the `harvester` ManagedChart. This prevents our changes applied in the next step from being reverted automatically.

   ```
   $ kubectl edit managedchart harvester -n fleet-local

   # edit spec.paused: true
   spec:
     paused: true
   ```

2. Edit the `harvester` Deployment object to make the cluster think itself is version 1.1.2

   ```
   $ kubectl edit deploy harvester -n harvester-system

         # add the env variable HARVESTER_SERVER_VERSION
         containers:
         - env:
           - name: HARVESTER_SERVER_VERSION
             value: "v1.1.2"
           - name: HARVESTER_SERVER_HTTPS_PORT
             value: "8443"
   ```

And initiate the upgrade as usual. When done, change the `harvester` Deployment by removing the `HARVESTER_SERVER_VERSION` env.