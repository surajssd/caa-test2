#
# SPDX-License-Identifier: Apache-2.0
#
ROOT_DIR := $(dir $(lastword $(MAKEFILE_LIST)))../../
include $(ROOT_DIR)podvm/Makefile.inc


.PHONY: image clean default-vpc

INSTANCE_TYPE ?= t3.small

VOLUME_SIZE ?= 30

image: $(IMAGE_FILE)

$(IMAGE_FILE): setopts $(BINARIES) $(FILES)
	mkdir -p toupload
	packer build ${OPTS} \
		-var region=${AWS_REGION} \
		-var instance_type=${INSTANCE_TYPE} \
		-var volume_size=${VOLUME_SIZE} \
		-var ami_name=${IMAGE_NAME} ./${PODVM_DISTRO}/
	rm -fr toupload

setopts:
ifeq ($(PODVM_DISTRO),)
	$(error PODVM_DISTRO is not defined)
endif
ifeq ($(AWS_REGION),)
	$(error AWS_REGION is not defined)
endif
ifeq ($(VPC_ID),)
	aws ec2 create-default-vpc --region ${AWS_REGION} || true
	aws ec2 wait vpc-available --region ${AWS_REGION}
else ifneq ($(SUBNET_ID),)
	$(eval OPTS += -var vpc_id=${VPC_ID} -var subnet_id=${SUBNET_ID} )
else
	$(error VPC_ID or SUBNET_ID not defined)
endif


clean:
	rm -f "$(IMAGE_FILE)" "$(UBUNTU_IMAGE_FILE)" $(BINARIES)
	rm -fr "$(SKOPEO_SRC)" "$(UMOCI_SRC)" "$(PAUSE_SRC)" "$(FILES_DIR)/$(PAUSE_BUNDLE)"

.PHONY: force
force:
