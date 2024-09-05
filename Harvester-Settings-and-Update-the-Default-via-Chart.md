
## Harvester Settings Definition

### CRD definition.

https://github.com/harvester/harvester/blob/3bba1d6dcc17589fe9607aff38bea7614065b8be/pkg/apis/harvesterhci.io/v1beta1/settings.go#L18C1-L35C2

```
type Setting struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	// +optional
	Value string `json:"value,omitempty"`

	// +optional
	Default string `json:"default,omitempty"`

	// +optional
	Customized bool `json:"customized,omitempty"`

	// +optional
	Source string `json:"source,omitempty"`

	Status SettingStatus `json:"status,omitempty"`
}
```

### Local object definition.

Harvester specific settings are defined in the source code, generally with the `Default` field.

https://github.com/harvester/harvester/blob/3bba1d6dcc17589fe9607aff38bea7614065b8be/pkg/settings/settings.go#L22

```
type Setting struct {
	Name     string
	Default  string
	ReadOnly bool
}

SupportBundleImage                     = NewSetting(SupportBundleImageName, "{}")
...
OvercommitConfig                       = NewSetting(OvercommitConfigSettingName, `{"cpu":1600,"memory":150,"storage":200}`)

```

## Harvester Settings Initialization and Sync

### Local object initialzation and CRD object sync.

https://github.com/harvester/harvester/blob/3bba1d6dcc17589fe9607aff38bea7614065b8be/pkg/controller/global/settings/register.go#L87

When Harvester POD starts, it will init the local `Setting` object and sync with CRD object `settings.harvesterhci.io`:

- If it is not eixsting, create a new one.

- If it is existing, use the potential harvester ENV like HARVESTER_OVERCOMMIT_CONFIG to replace the `Value` field of `overcommit-config` settings. note the ENV naming. If the local object `Default` != `Default` of `settings.harvesterhci.io`, replace it.


### How to deal with the dynamic `Default`? Use ENV `HARVESTER_setting_name_to_uppercase_DEFAULT_VALUE`.

Settings like `support-bundle-image` has a dynamic `Default` value, which is defined in Harvester chart, each Harvester releases has a different value.

https://github.com/harvester/harvester/blob/07610a9958784aa127b578bf83fa4fb3b835d60b/deploy/charts/harvester/values.yaml#L489
```
  image:
    imagePullPolicy: IfNotPresent
    repository: rancher/support-bundle-kit
    tag: v0.0.40
```

https://github.com/harvester/harvester/blob/07610a9958784aa127b578bf83fa4fb3b835d60b/deploy/charts/harvester/templates/deployment.yaml#L79

https://github.com/harvester/harvester/blob/07610a9958784aa127b578bf83fa4fb3b835d60b/deploy/charts/harvester/templates/_helpers.tpl#L121

We can define an ENV like `HARVESTER_SUPPORT_BUNDLE_IMAGE_DEFAULT_VALUE` on Harvester deployment, when Harvester POD starts, it will use this to replace the setting `support-bundle-image` Default field.

### Summarize

Harvester deployment can have injected ENV:

- Env like `HARVESTER_OVERCOMMIT_CONFIG` is used to replace setting `Value` field.

- Env like `HARVESTER_SUPPORT_BUNDLE_IMAGE_DEFAULT_VALUE` is used to replace setting `Default` field.

Note the ENV naming.

## Harvester Settings Usage

When a controller uses a settings.harvesterhci.io, the general idea is:

```
value := setting.Value

if value == "" {
	value = setting.Default
}

// the `value` can still be `""` as Default can also be `""`
// if it is used as formats like JSON string, it can be replaced with `"{}"`
// to meet json.Unmarshal
if value == "" {
	value = "{}"
}

// continue the processing

```