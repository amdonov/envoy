load("@rules_cc//cc:defs.bzl", "cc_library")

licenses(["notice"])  # Apache 2

cc_library(
    name = "crypto",
    srcs = [
        "crypto/libcrypto.a",
    ],
    hdrs = glob(["include/openssl/*.h"]),
    includes = ["include"],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "ssl",
    srcs = [
        "ssl/libssl.a",
    ],
    hdrs = glob(["include/openssl/*.h"]),
    includes = ["include"],
    visibility = ["//visibility:public"],
    deps = [":crypto"],
)

genrule(
    name = "build",
    srcs = glob(["**"]),
    outs = [
        "crypto/libcrypto.a",
        "ssl/libssl.a",
    ],
    cmd = select({
        "@platforms//cpu:x86_64": "export CC=\"$$(realpath $(CC))\" && $(location @envoy//bazel/external:aws_lc.genrule_cmd) $(location @sysroot_linux_amd64//:WORKSPACE) $(location crypto/libcrypto.a) $(location ssl/libssl.a)",
        "@platforms//cpu:aarch64": "export CC=\"$$(realpath $(CC))\" && $(location @envoy//bazel/external:aws_lc.genrule_cmd) $(location @sysroot_linux_arm64//:WORKSPACE) $(location crypto/libcrypto.a) $(location ssl/libssl.a)",
    }),
    toolchains = ["@bazel_tools//tools/cpp:current_cc_toolchain"],
    tools = [
        "@envoy//bazel/external:aws_lc.genrule_cmd",
    ] + select({
        "@platforms//cpu:x86_64": [
            "@sysroot_linux_amd64//:WORKSPACE",
            "@sysroot_linux_amd64//:sysroot",
        ],
        "@platforms//cpu:aarch64": [
            "@sysroot_linux_arm64//:WORKSPACE",
            "@sysroot_linux_arm64//:sysroot",
        ],
        "//conditions:default": [],
    }),
)
