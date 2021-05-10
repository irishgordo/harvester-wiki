# Adding new API object types and controllers

Here's an example of how to add new API object types and controllers. Assume that we want a new resource type called DownloadURL. If a DownloadURL resource is created, the Harvester server tries to download from that URL.

1. Add a `/pkg/apis/harvesterhci.io/v1beta1/downloadurl.go` file
```golang
package v1beta1

import (
	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
)

// +genclient
// +k8s:deepcopy-gen:interfaces=k8s.io/apimachinery/pkg/runtime.Object
// +kubebuilder:printcolumn:name="URL",type="string",JSONPath=`.url`

type DownloadURL struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	// +kubebuilder:validation:Required
	URL string `json:"url,omitempty"`
}
```

2. Add the DownloadURL type to `/pkg/codegen/main.go`.
```golang
	controllergen.Run(args.Options{
		OutputPackage: "github.com/harvester/harvester/pkg/generated",
		Boilerplate:   "scripts/boilerplate.go.txt",
		Groups: map[string]args.Group{
			"harvesterhci.io": {
				Types: []interface{}{
					harvesterv1.DownloadURL{},
					harvesterv1.KeyPair{},
					harvesterv1.Preference{},
					//...
				},
				GenerateTypes:   true,
				GenerateClients: true,
			},
		},
	})
```
3. Run `go generate`. It will generate codes for clients, controllers, CRDs, etc.
```sh
$ go generate
# some output...
$ git status
On branch downloadurl
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   pkg/apis/harvesterhci.io/v1beta1/zz_generated_deepcopy.go
	modified:   pkg/apis/harvesterhci.io/v1beta1/zz_generated_list_types.go
	modified:   pkg/apis/harvesterhci.io/v1beta1/zz_generated_register.go
	modified:   pkg/generated/clientset/versioned/typed/harvesterhci.io/v1beta1/fake/fake_harvesterhci.io_client.go
	modified:   pkg/generated/clientset/versioned/typed/harvesterhci.io/v1beta1/generated_expansion.go
	modified:   pkg/generated/clientset/versioned/typed/harvesterhci.io/v1beta1/harvesterhci.io_client.go
	modified:   pkg/generated/controllers/harvesterhci.io/v1beta1/interface.go
	modified:   vendor/github.com/rancher/dynamiclistener/storage/kubernetes/controller.go

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	deploy/charts/harvester/crds/harvesterhci.io_downloadurls.yaml
	pkg/generated/clientset/versioned/typed/harvesterhci.io/v1beta1/downloadurl.go
	pkg/generated/clientset/versioned/typed/harvesterhci.io/v1beta1/fake/fake_downloadurl.go
	pkg/generated/controllers/harvesterhci.io/v1beta1/downloadurl.go
```

By applying the CRD or add it to `pkg/controller/crds/setup.go` as the following, DownloadURL resource is available in Harvester APIs.
```golang
factory.BatchCreateCRDsIfNotExisted(
			crd.FromGV(harvesterv1.SchemeGroupVersion, "DownloadURL"),
			//...
			createNetworkAttachmentDefinitionCRD(),
		)
```


![image](https://user-images.githubusercontent.com/5697937/117624775-db30df00-b1a7-11eb-9e18-0c5d1f0ebfd8.png)


4. Now we add a controller to implement the download logic. Add a `pkg/controller/master/downloadurl/controller.go` file:
```golang
package downloadurl

import (
	"context"
	"net/http"
	"time"

	"github.com/sirupsen/logrus"

	"github.com/harvester/harvester/pkg/apis/harvesterhci.io/v1beta1"
	"github.com/harvester/harvester/pkg/config"
)

const (
	controllerName = "downloadurl-controller"
)

func Register(ctx context.Context, management *config.Management, options config.Options) error {
	downloadURLController := management.HarvesterFactory.Harvesterhci().V1beta1().DownloadURL()
	handler := &handler{
		httpClient: http.Client{
			Timeout: 15 * time.Second,
		},
	}
	downloadURLController.OnChange(ctx, controllerName, handler.OnChange)
	return nil
}

// handler tries to download from specified URL on DownloadURL changes.
type handler struct {
	httpClient http.Client
}

func (h *handler) OnChange(_ string, downloadURL *v1beta1.DownloadURL) (*v1beta1.DownloadURL, error) {
	if downloadURL == nil || downloadURL.DeletionTimestamp != nil {
		return nil, nil
	}

	logrus.Debugf("download from URL: %s", downloadURL.URL)
	resp, err := h.httpClient.Get(downloadURL.URL)
	if err != nil {
		return nil, err
	}
	defer resp.Body.Close()

	// then operates the response body

	return downloadURL, nil
}
```

5. Register the controller in `pkg/controller/master/setup.go`.
```

var registerFuncs = []registerFunc{
	// ...
	downloadurl.Register,
}
```

6. Now we can run Harvester with the above changes. Then we can apply a DownloadURL resource and observe the log
```
$ cat downloadurl.yaml
apiVersion: harvesterhci.io/v1beta1
kind: DownloadURL
metadata:
  name: test
url: https://harvesterhci.io
$ kubectl apply -f downloadurl.yaml
```

```
# Logs from Harvester server
...
DEBU[0279] download from URL: https://harvesterhci.io   
...
```

7. That's it! You've successfully added a new API resource and a controller, then observe how the controller works on CRD changes!