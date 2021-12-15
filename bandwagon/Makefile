MKFILE_PATH := $(abspath $(lastword $(MAKEFILE_LIST)))
CURRENT_DIR := $(realpath $(patsubst %/,%,$(dir $(MKFILE_PATH))))
ROOT_DIR := $(realpath $(CURRENT_DIR)/..)

export VERSION ?= $(shell $(CURRENT_DIR)/version.sh)
NAME := bandwagon
PACKAGE := gravitational.io/$(NAME):$(VERSION)
PACKAGE_FILENAME := $(NAME)-$(VERSION).tar.gz
OPS_URL ?= https://opscenter.localhost.localdomain:33009

GRAVITY ?= gravity
DOCKER ?= docker
GO ?= go

BUILD_DIR_CONTAINER := _build
BUILD_DIR := $(CURRENT_DIR)/_build
WEB_APP_DIR := $(CURRENT_DIR)/web

BUILDBOX_IMAGE ?= quay.io/gravitational/debian-venti:go1.14.13-buster
BUILDBOX_DIR ?= /gopath/src/github.com/gravitational

.PHONY: all
all: import

.PHONY: import
import: build
	$(GRAVITY) --insecure app delete $(PACKAGE) --force --ops-url=$(OPS_URL) && \
	$(GRAVITY) --insecure app import ./app --vendor --ops-url=$(OPS_URL) \
		--version=$(VERSION) --set-image=$(NAME):$(VERSION) \
		--set-image=bandwagon-hook:$(VERSION)

.PHONY: build
build: web-build go-build hook-build
	@docker build -t $(NAME):$(VERSION) .

.PHONY: web-build
web-build:
	$(MAKE) -C $(WEB_APP_DIR) docker-build

.PHONY: go-build
go-build: | $(BUILD_DIR)
	@docker run -i --rm=true -v $(ROOT_DIR):$(BUILDBOX_DIR) \
		$(BUILDBOX_IMAGE) /bin/bash -c "make -C $(BUILDBOX_DIR)/bandwagon go-build-in-buildbox"

.PHONY: hook-build
hook-build:
	$(MAKE) -C images hook

.PHONY: go-build-in-buildbox
go-build-in-buildbox:
	@go build -mod=vendor -o $(BUILD_DIR_CONTAINER)/$(NAME)

$(BUILD_DIR):
	@mkdir -p $@
