TAG=$(shell git describe --always)
ifeq ($(GITHUB_ACTIONS),true)
DOCKER_REGISTRY?=ghcr.io
DOCKER_USER?=$(GITHUB_REPOSITORY_OWNER)
else
DOCKER_REGISTRY?=docker.io
DOCKER_USER?=wsinpg
endif

#DOCKER_REGISTRY=ghcr.io
#DOCKER_USER=wtsi-npg

NOW=$(shell date --utc --iso-8601=seconds)

DOCKER_PREFIX?=$(DOCKER_REGISTRY)/$(DOCKER_USER)
DOCKER_ARGS ?= --progress=plain --rm

.PHONY: clean push

image_names := samtools

git_url=$(shell git remote get-url origin)
git_commit=$(shell git log --pretty=format:'%H' -n 1)

images := $(addsuffix .$(TAG), $(image_names))
remote := $(addsuffix .$(TAG).pushed, $(image_names))

all: $(images)

push: $(remote)

samtools.$(TAG): Dockerfile
	docker buildx build $(DOCKER_ARGS) \
	--load \
	--build-context singularity=../singularity \
	--build-arg BASE_IMAGE=$(DOCKER_PREFIX)/ub-16.04-base \
	--build-arg DOCKER_IMAGE=$(subst .$(TAG),,$@) \
	--build-arg DOCKER_TAG=$(TAG) \
	--build-arg IRODS_VERSION=4.2.11 \
	--build-arg BATON_VERSION=4.2.1 \
	--build-arg HTSLIB_VERSION=1.21 \
	--build-arg SAMTOOLS_VERSION=1.21 \
	--build-arg BCFTOOLS_VERSION=1.21 \
	--build-arg DEFLATE_VERSION=1.20 \
	--label org.opencontainers.image.title=$(image_names) \
	--label org.opencontainers.image.source=$(git_url) \
	--label org.opencontainers.image.revision=$(git_commit) \
	--label org.opencontainers.image.version=$(TAG) \
	--label org.opencontainers.image.created=$(NOW) \
	--label org.opencontainers.image.vendor=npg.sanger.ac.uk \
	--tag $(DOCKER_PREFIX)/samtools:latest \
	--tag $(DOCKER_PREFIX)/samtools:$(TAG) --file $< .
	touch $@

%.$(TAG).pushed: %.$(TAG)
	docker push $(DOCKER_PREFIX)/$*:$(TAG)
	docker push $(DOCKER_PREFIX)/$*:latest
	touch $@

remote-clean:
	rm -f $(remote)

clean:
	rm -f $(foreach image_name,$(image_names), $(image_name)*)
