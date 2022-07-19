SHELL := bash

ZILD := \
    clean \
    cpan \
    cpanshell \
    dist \
    distdir \
    distshell \
    disttest \
    install \
    release \
    update \

DOCKER_IMAGE := alpine-test-yamlscript-perl

test ?= test/


#------------------------------------------------------------------------------
default:

.PHONY: test
test:
	prove -v $(test)

$(ZILD):
	zild $@

docker-test: docker-build dist
	docker run --rm -it \
	    -v $(PWD):/host \
	    -w /host \
	    $(DOCKER_IMAGE) \
	    bash test/docker-cpan-test.sh

docker-shell: docker-build dist
	touch /tmp/yamlscript-docker-test-history; \
	docker run --rm -it \
	    -v /tmp/yamlscript-docker-test-history:/root/.bash_history \
	    -v $(PWD):/host \
	    -w /host \
	    $(DOCKER_IMAGE) \
	    bash

docker-build:
	docker build -t $(DOCKER_IMAGE) .

gha-tmate:
	@curl -X POST -H "Accept: application/vnd.github+json" -H "Authorization: token $$(grep api-token ~/.git-hub/config | awk '{print $$3}')" https://api.github.com/repos/ingydotnet/yamlscript/actions/workflows/debug-with-tmate.yaml/dispatches -d '{"ref":"debug-with-tmate","inputs":{"runs-on":"windows-2022"}}'
	@echo 'GHA debug-with-tmate workflow started.'
	@echo 'Go to: https://github.com/ingydotnet/yamlscript/actions'

