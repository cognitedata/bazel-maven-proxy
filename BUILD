package(default_visibility = ["//visibility:private"])

load("@io_bazel_rules_docker//java:image.bzl", "java_image")
load("@io_bazel_rules_docker//container:container.bzl", "container_push", "container_image")
load("@bazel_tools//tools/build_defs/pkg:pkg.bzl", "pkg_tar")

java_binary(
    name = "maven_proxy",
    main_class = "com.salesforce.bazel.maven.proxy.server.MavenProxyServer",
    runtime_deps = [
    	"//server"
    ],
    visibility = ["//visibility:public"],
)

java_image(
    name = "maven_proxy_image",
    base = "@java_base_11//image",
    main_class = "com.salesforce.bazel.maven.proxy.server.MavenProxyServer",
    runtime_deps = [
    	"//server:server"
    ],
    visibility = ["//visibility:public"],
    args = [
        "--unsecure-port=5000",
        "-c", "/app/config/config.yml",
        "--maven-settings=/maven-credentials/settings.xml",
        "--local-maven-repository=/tmp"
    ],
)

filegroup(
    name = "proxy",
    srcs = ["proxy.sh"],
)

container_image(
    name = "container_image",
    base = ":maven_proxy_image",
    ports = ["5000"],
    directory = "/app",
    workdir = "/app",
    legacy_run_behavior = False,
    files = [":proxy"],
    entrypoint = [
    "sh",
    "-c",
    "cat"],
)

container_push(
    name = "container_push_dev",
    image = ":container_image",
    format = "Docker",
    registry = "eu.gcr.io",
    repository = "cognitedata-development/bazel-maven-proxy",
    tag = "dev-{BUILD_USER}",
)

container_push(
    name = "container_push",
    image = ":container_image",
    format = "Docker",
    registry = "eu.gcr.io",
    repository = "cognitedata/bazel-maven-proxy",
    tag = "latest",
)
