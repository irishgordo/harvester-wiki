## Create a GitHub milestone and Zenhub release

- Create a GitHub milestone, for example, `v1.4.0`.
- Create a Zenhub release, for example, `v1.4.0`. Note the GitHub milestone and Zenhub release name and due date should be identical.

## Create stable branches

We need to create stable branches in several repositories. This is a one-time work for every milestone.

> Note: the consequence of having a stable branch is developers need to do a backport when a change is merged in the `master` branch. In general, before the first release candidate of the milestone, I (as a release captain) periodically sync changes from the `master` branch to the stable branch to make engineers’ lives easier.
> 

The following example assumes we are going to release Harvester `v1.4.0` soon.

- Harvester components
    - Components include dependency charts and addons.
    - Component owners have to create a stable branch.
    - For each component, the component owner has to bump the minor version. This applies to both component charts and image tags.
        - For example, in Harvester `v1.3.x`, the harvester-node-disk-manager component has
            - chart version `0.6.0` https://github.com/harvester/harvester/blob/4e7387e8611c36f8165f331f74b17ae212fbddf3/deploy/charts/harvester/Chart.yaml#L33
            - image tag `v0.6.3` https://github.com/harvester/harvester/blob/f3fb7d2e353cef422617e2d3629644758fd28f75/deploy/charts/harvester/values.yaml#L377
            
            For milestone `v1.4.0`, the harvester-node-disk-manager component has to
            
            - Bump chart version to `0.7.0`
            - Bump the image tag to `v0.7.0`
        - A sample PR to triage the works to component owners: https://github.com/harvester/harvester/issues/6158
    
    > NOTE: there is an ongoing discussion about whether we should sync the components’ minor version with the Harvester’s minor version. For example, both use v1.4.x in the above example.
    > 
- [[harvester/addons](https://github.com/harvester/addons/tree/main)](https://github.com/harvester/addons/tree/main)
    - Create a new stable branch `v1.4`.
    - Submit a PR to change versions in this branch
        - https://github.com/harvester/addons/blob/main/version_info
        - https://github.com/harvester/addons/blob/main/pkg/templates/rancherd-22-addons.yaml
- [[harvester/harvester](https://github.com/harvester/harvester)](https://github.com/harvester/harvester)
    - [[Create a new stable branch](https://github.com/harvester/harvester/branches)](https://github.com/harvester/harvester/branches) `v1.4`, from the `master` branch.
- [[harvester/harvester-installer](https://github.com/harvester/harvester-installer)](https://github.com/harvester/harvester-installer)
    - [[Create a new stable](https://github.com/harvester/harvester-installer/branches)](https://github.com/harvester/harvester-installer/branches) branch `v1.4` from the `master` branch.
    - For this new branch
        - Update harvester branch
            - https://github.com/harvester/harvester-installer/blob/6267f1893b02b4302d1b7b924f7d378b8cce47d9/scripts/build#L14
            - https://github.com/harvester/harvester-installer/blob/6267f1893b02b4302d1b7b924f7d378b8cce47d9/scripts/build-bundle#L43
        - Update add-ons branch (since v1.4)
            - https://github.com/harvester/harvester-installer/blob/8ed87a5023061308b575227125a351061e6d5161/scripts/build#L33
            - https://github.com/harvester/harvester-installer/blob/8ed87a5023061308b575227125a351061e6d5161/scripts/build-bundle#L15
        - Sample PR: https://github.com/harvester/harvester-installer/pull/761
- [[harvester/os2](https://github.com/harvester/os2)](https://github.com/harvester/os2)
    - Create new projects in OBS
        - https://build.opensuse.org/project/show/isv:Rancher:Harvester:OS:v1.4
        - https://build.opensuse.org/project/show/isv:Rancher:Harvester:ExtraPackages:v1.4
    - Create a new stable branch `harvester-v1.4.x`.
    - Update versions: https://github.com/harvester/os2/pull/116/files
    - Update the action code to create addon: https://github.com/harvester/os2/pull/118
    - Tag a new version, CI will create PRs.
        - harvester/harvester-installer: a PR to bump OS
        - harvester/harvester: a PR to bump Nvidia driver addon.
        - Ask some developers to review and merge the PRs.
- [[harvester/dashboard](https://github.com/harvester/dashboard)](https://github.com/harvester/dashboard)
    - Ask the UI developer to create the `release-harvester-v1.4` branch.
- [[harvester/harvester](https://github.com/harvester/harvester)](https://github.com/harvester/harvester)
    - For this new branch
        - Update installer branch: https://github.com/harvester/harvester/blob/95b4f9dc3c63421bcb5cfff5fd579c0cee7424b3/scripts/build-iso#L10
        - Update UI-indexes in the **settings** and **Dockerfile**, like https://github.com/harvester/harvester/pull/5857/files
        - Update addons branch (since v1.4)
            - https://github.com/harvester/harvester/blob/9c0a3a17f42831b590a92f3743ac027a66a25b8d/scripts/generate-addons#L8
        - Update components’ image tags [[in the values.yaml file](https://github.com/harvester/harvester/blob/master/deploy/charts/harvester/values.yaml)](https://github.com/harvester/harvester/blob/master/deploy/charts/harvester/values.yaml)
            - Sample: https://github.com/harvester/harvester/pull/6128/commits/3f0e9ed94272bd555245e353ba17a3bd7e732913
            - An easier way is to copy the file from the previous version’s stable branch