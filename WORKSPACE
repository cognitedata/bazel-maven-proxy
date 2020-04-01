workspace(name= "bazel_maven_proxy")

# To find the sha256 for an http_archive, run wget on the URL to download the
# file, and use sha256sum on the file to produce the sha256.

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# bazel-skylib 0.8.0 released 2019.03.20 (https://github.com/bazelbuild/bazel-skylib/releases/tag/0.8.0)
skylib_version = "0.8.0"
http_archive(
    name = "bazel_skylib",
    type = "tar.gz",
    url = "https://github.com/bazelbuild/bazel-skylib/releases/download/{}/bazel-skylib.{}.tar.gz".format (skylib_version, skylib_version),
    sha256 = "2ef429f5d7ce7111263289644d233707dba35e39696377ebab8b0bc701f7818e",
)

# check minimum Bazel version
load("@bazel_skylib//lib:versions.bzl", "versions")
versions.check(minimum_bazel_version = "0.25.1")


# maven_install
RULES_JVM_EXTERNAL_TAG = "2.1"
RULES_JVM_EXTERNAL_SHA = "515ee5265387b88e4547b34a57393d2bcb1101314bcc5360ec7a482792556f42"
http_archive(
    name = "rules_jvm_external",
    strip_prefix = "rules_jvm_external-%s" % RULES_JVM_EXTERNAL_TAG,
    sha256 = RULES_JVM_EXTERNAL_SHA,
    url = "https://github.com/bazelbuild/rules_jvm_external/archive/%s.zip" % RULES_JVM_EXTERNAL_TAG,
)
load("@rules_jvm_external//:defs.bzl", "maven_install")

# Maven Dependencies
maven_install(
    name = "maven",
    artifacts = [
		"info.picocli:picocli:3.8.2",
		"org.apache.commons:commons-compress:1.18",
		"org.eclipse.jetty.http2:http2-client:9.4.18.v20190429",
		"org.eclipse.jetty.http2:http2-http-client-transport:9.4.18.v20190429",
		"org.eclipse.jetty.http2:http2-server:9.4.18.v20190429",
		"org.eclipse.jetty:jetty-alpn-java-server:9.4.18.v20190429",
		"org.eclipse.jetty:jetty-alpn-server:9.4.18.v20190429",
		"org.eclipse.jetty:jetty-proxy:9.4.18.v20190429",
		"org.eclipse.jetty:jetty-server:9.4.18.v20190429",
		"org.eclipse.jetty:jetty-servlet:9.4.18.v20190429",
		"org.junit.jupiter:junit-jupiter-api:5.3.2",
		"org.junit.jupiter:junit-jupiter-engine:5.3.2",
		"org.slf4j:slf4j-api:1.7.25",
		"org.slf4j:slf4j-simple:1.7.25",
		"org.yaml:snakeyaml:1.24",
    ],
    repositories = [
	    "https://jcenter.bintray.com/",
	    "https://maven.google.com",
	    "https://repo1.maven.org/maven2",
    ],
)

http_archive(
    name = "io_bazel_rules_docker",
    sha256 = "e513c0ac6534810eb7a14bf025a0f159726753f97f74ab7863c650d26e01d677",
    strip_prefix = "rules_docker-0.9.0",
    urls = ["https://github.com/bazelbuild/rules_docker/releases/download/v0.9.0/rules_docker-v0.9.0.tar.gz"],
)

load("@io_bazel_rules_docker//repositories:repositories.bzl", container_repositories = "repositories")

container_repositories()

load("@io_bazel_rules_docker//java:image.bzl", _java_image_repos = "repositories")

_java_image_repos()

load("@io_bazel_rules_docker//container:container.bzl", "container_pull",)

container_pull(
	name = "java_base_11",
	registry = "gcr.io",
	repository = "distroless/java",
	digest = "sha256:8c2779a0aa6ba72ed80eeb9263849ddc84eef559b13e5311e080c2614f11ea15",
)