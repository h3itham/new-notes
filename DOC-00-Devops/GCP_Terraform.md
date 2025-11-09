- Prerquiste install `terraform` and `gcloud`
- Authenticate `gcloud` with your GCP account. 
	```bash 
	 gcloud auth application-default login
	```

#### Configure & test GCP 
1. Create simple VM for test. 
	```hcl 
	provider "google" {
	  project = "gcloud-cli-456308"
	  region  = "us-central1"
	  zone    = "us-central1-a"
	}
	
	resource "google_compute_instance" "default" {
	  name         = "terraform-vm"
	  machine_type = "e2-micro"
	  zone         = "us-central1-a"
	
	  boot_disk {
	    initialize_params {
	      image = "debian-cloud/debian-11"
	    }
	  }
	
	  network_interface {
	    network = "default"
	    access_config {}  # Assigns a public IP
	  }
	}
	```

### Create GKE Cluster 
1. You can refer to the following article and create every thing. 
	```bash 
	https://developer.hashicorp.com/terraform/tutorials/kubernetes/gke
	```

