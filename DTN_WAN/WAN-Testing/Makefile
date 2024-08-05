### Generic Variables
SHELL := /bin/zsh

.PHONY: help
help: ## Display help message (*: main entry points / []: part of an entry point)
	@grep -E '^[0-9a-zA-Z_-]+\.*[0-9a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'


################################################################################
# AVD Lab Testing
################################################################################

.PHONY: install-collections
install-collections: ## Install ansible collections
	# ansible-galaxy collection install git+https://github.com/gmuloc/avd.git#/ansible_collections/arista/avd/,fix-make-wan-rr-rr-clients --force
	# ansible-galaxy collection install arista.avd --pre --force
	ansible-galaxy collection install git+https://github.com/aristanetworks/ansible-avd.git#/ansible_collections/arista/avd/,devel --force
	# ansible-galaxy collection install git+https://github.com/ClausHolbechArista/ansible-avd.git#/ansible_collections/arista/avd/,feat/eos_designs/filter-vrf --force
	# ansible-galaxy collection install git+https://github.com/gmuloc/avd.git#/ansible_collections/arista/avd/,fix-make-wan-rr-rr-clients --force
	# ansible-galaxy collection install community.general --force


.PHONY: fabric-build
fabric-build: ## Build fabric artifacts
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/DC1/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/SITE1/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/SITE2/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/SITE3/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/SITE4/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/PATHFINDERS/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/CLOUDS/inventory.yml
	ansible-playbook playbooks/fabric-build.yml -i ./inventories/TEST_HOST/inventory.yml

.PHONY: fabric-deploy-eapi
fabric-deploy-eapi: ## Deploy config via eAPI
	ansible-playbook playbooks/fabric-deploy-eapi.yml -i ./inventories/DC1/inventory.yml
	ansible-playbook playbooks/fabric-deploy-eapi.yml -i ./inventories/SITE1/inventory.yml
	ansible-playbook playbooks/fabric-deploy-eapi.yml -i ./inventories/PATHFINDERS/inventory.yml
	ansible-playbook playbooks/fabric-deploy-eapi.yml -i ./inventories/CLOUDS/inventory.yml
	ansible-playbook playbooks/fabric-deploy-eapi.yml -i ./inventories/TEST_HOST/inventory.yml