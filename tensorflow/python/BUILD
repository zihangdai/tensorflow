# Description:
# Python support for TensorFlow.

package(default_visibility = ["//tensorflow:internal"])

licenses(["notice"])  # Apache 2.0

exports_files(["LICENSE"])

load("//tensorflow:tensorflow.bzl", "tf_cuda_library")
load("//tensorflow:tensorflow.bzl", "tf_gen_op_wrapper_py")
load("//tensorflow:tensorflow.bzl", "py_tests")
load("//tensorflow:tensorflow.bzl", "tf_py_wrap_cc")
load("//tensorflow:tensorflow.bzl", "cuda_py_test")
load("//tensorflow:tensorflow.bzl", "cuda_py_tests")
load("//tensorflow/core:platform/default/build_config.bzl", "tf_proto_library")
load("//tensorflow/core:platform/default/build_config.bzl", "tf_proto_library_py")
load("//tensorflow/core:platform/default/build_config.bzl", "tf_additional_lib_deps")

py_library(
    name = "python",
    srcs = [
        "__init__.py",
    ],
    srcs_version = "PY2AND3",
    visibility = ["//tensorflow:__pkg__"],
    deps = [
        ":client",
        ":client_testlib",
        ":framework",
        ":framework_test_lib",
        ":kernel_tests/gradient_checker",
        ":platform",
        ":platform_test",
        ":summary",
        ":training",
        "//tensorflow/contrib:contrib_py",
    ],
)

py_library(
    name = "platform",
    srcs = glob(["platform/*.py"]),
    srcs_version = "PY2AND3",
    deps = [
        ":pywrap_tensorflow",
        "//tensorflow/core:protos_all_py",
    ],
)

py_library(
    name = "platform_test",
    srcs = [
        "platform/googletest.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":platform",
        ":timeline",
    ],
)

py_tests(
    name = "platform_tests",
    size = "small",
    srcs = glob(["platform/*_test.py"]),
    additional_deps = [
        ":platform",
        ":platform_test",
    ],
)

cc_library(
    name = "numpy_lib",
    srcs = ["lib/core/numpy.cc"],
    hdrs = [
        "lib/core/numpy.h",
    ],
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//third_party/py/numpy:headers",
        "//util/python:python_headers",
    ],
)

cc_library(
    name = "py_func_lib",
    srcs = ["lib/core/py_func.cc"],
    hdrs = [
        "lib/core/py_func.h",
    ],
    deps = [
        ":numpy_lib",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:script_ops_op_lib",
        "//third_party/py/numpy:headers",
        "//util/python:python_headers",
    ],
)

cc_library(
    name = "py_record_reader_lib",
    srcs = [
        "lib/io/py_record_reader.cc",
    ],
    hdrs = [
        "lib/io/py_record_reader.h",
    ],
    deps = [
        "//tensorflow/core:lib",
    ],
)

cc_library(
    name = "py_record_writer_lib",
    srcs = [
        "lib/io/py_record_writer.cc",
    ],
    hdrs = [
        "lib/io/py_record_writer.h",
    ],
    deps = [
        "//tensorflow/core:lib",
    ],
)

cc_binary(
    name = "framework/test_file_system.so",
    srcs = ["framework/test_file_system.cc"],
    linkopts = select({
        "//conditions:default": [
            "-lm",
        ],
        "//tensorflow:darwin": [],
    }),
    linkshared = 1,
    deps = [
        "//tensorflow/core:framework_headers_lib",
        "@protobuf//:protobuf",
    ],
)

py_test(
    name = "file_system_test",
    size = "small",
    srcs = ["framework/file_system_test.py"],
    data = [":framework/test_file_system.so"],
    main = "framework/file_system_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        "//tensorflow:tensorflow_py",
    ],
)

cc_library(
    name = "python_op_gen",
    srcs = [
        "framework/python_op_gen.cc",
        "framework/python_op_gen.h",
    ],
    visibility = ["//visibility:public"],
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_cc",
    ],
    alwayslink = 1,
)

cc_library(
    name = "python_op_gen_main",
    srcs = [
        "framework/python_op_gen_main.cc",
    ],
    visibility = ["//visibility:public"],
    deps = [
        ":python_op_gen",
    ],
)

# What is needed for tf_gen_op_wrapper_py.
py_library(
    name = "framework_for_generated_wrappers",
    srcs = [
        "framework/device.py",
        "framework/dtypes.py",
        "framework/function.py",
        "framework/op_def_registry.py",
        "framework/ops.py",
        "framework/registry.py",
        "framework/tensor_shape.py",
        "framework/versions.py",
        # TODO(josh11b): Move this to the framework directory
        "ops/op_def_library.py",
        "ops/constant_op.py",
    ],
    srcs_version = "PY2AND3",
    visibility = ["//visibility:public"],
    deps = [
        ":platform",
        ":util",
        "//tensorflow/core:protos_all_py",
    ],
)

py_library(
    name = "framework",
    srcs = [
        # TODO(mrry): Move this to the framework directory.
        "client/graph_util.py",
        "framework/errors.py",
        "framework/framework_lib.py",
        "framework/importer.py",
        "framework/random_seed.py",
        "framework/tensor_util.py",
        "framework/load_library.py",
        # TODO(josh11b): Move this to the framework directory
        "ops/common_shapes.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":framework_for_generated_wrappers",
        ":pywrap_tensorflow",
    ],
)

# load("//third_party/py/cython:build_defs.bzl", "pyx_library")

py_library(
    name = "extra_py_tests_deps",
    srcs_version = "PY2AND3",
    deps = ["//tensorflow:tensorflow_py"],
)

py_library(
    name = "framework_test_lib",
    srcs = [
        "framework/test_util.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":framework",
        ":platform_test",
        ":pywrap_tensorflow",
        ":session",
        ":util",
    ],
)

py_library(
    name = "client_testlib",
    srcs = [
        "platform/test.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
    ],
)

py_test(
    name = "framework_errors_test",
    size = "small",
    srcs = ["framework/errors_test.py"],
    main = "framework/errors_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
        "//tensorflow:tensorflow_py",
        "//tensorflow/core:protos_all_py",
    ],
)

py_test(
    name = "contrib_test",
    size = "small",
    srcs = ["framework/contrib_test.py"],
    main = "framework/contrib_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        "//tensorflow:tensorflow_py",
    ],
)

py_test(
    name = "proto_test",
    size = "small",
    srcs = ["framework/proto_test.py"],
    main = "framework/proto_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        "//tensorflow:tensorflow_py",
    ],
)

tf_gen_op_wrapper_py(
    name = "functional_ops",
    out = "ops/gen_functional_ops.py",
    hidden = [
        "SymbolicGradient",
    ],
)

py_library(
    name = "functional_ops_lib",
    srcs = ["ops/functional_ops.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":functional_ops",
    ],
)

cuda_py_tests(
    name = "framework_function_test",
    size = "medium",
    srcs = ["framework/function_test.py"],
    additional_deps = [
        ":functional_ops_lib",
    ],
)

py_test(
    name = "framework_versions_test",
    size = "small",
    srcs = ["framework/versions_test.py"],
    main = "framework/versions_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
        "//tensorflow:tensorflow_py",
    ],
)

py_test(
    name = "framework_importer_test",
    size = "small",
    srcs = ["framework/importer_test.py"],
    main = "framework/importer_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":ops",
        ":platform_test",
        "//tensorflow:tensorflow_py",
    ],
)

tf_gen_op_wrapper_py(
    name = "test_ops",
    out = "framework/test_ops.py",
    deps = [":test_ops_kernels"],
)

cc_library(
    name = "test_ops_kernels",
    srcs = ["framework/test_ops.cc"],
    linkstatic = 1,
    deps = ["//tensorflow/core:framework"],
    alwayslink = 1,
)

py_test(
    name = "framework_ops_test",
    size = "small",
    srcs = ["framework/ops_test.py"],
    main = "framework/ops_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":ops",
        ":platform_test",
        ":session",
        ":test_ops",
        "//tensorflow/core:protos_all_py",
    ],
)

py_test(
    name = "framework_tensor_shape_test",
    size = "small",
    srcs = ["framework/tensor_shape_test.py"],
    main = "framework/tensor_shape_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
        "//tensorflow/core:protos_all_py",
    ],
)

py_test(
    name = "framework_device_test",
    size = "small",
    srcs = ["framework/device_test.py"],
    main = "framework/device_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
        "//tensorflow/core:protos_all_py",
    ],
)

py_test(
    name = "framework_tensor_shape_div_test",
    size = "small",
    srcs = ["framework/tensor_shape_div_test.py"],
    main = "framework/tensor_shape_div_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
        "//tensorflow/core:protos_all_py",
    ],
)

py_test(
    name = "framework_tensor_util_test",
    size = "small",
    srcs = ["framework/tensor_util_test.py"],
    main = "framework/tensor_util_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":ops",
        ":platform_test",
        "//tensorflow:tensorflow_py",
    ],
)

py_test(
    name = "framework_test_util_test",
    size = "small",
    srcs = ["framework/test_util_test.py"],
    main = "framework/test_util_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":ops",
        ":platform_test",
        "//tensorflow:tensorflow_py",
    ],
)

py_test(
    name = "framework_dtypes_test",
    size = "small",
    srcs = ["framework/dtypes_test.py"],
    main = "framework/dtypes_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":platform_test",
        "//tensorflow:tensorflow_py",
        "//tensorflow/core:protos_all_py",
    ],
)

py_test(
    name = "op_def_library_test",
    size = "small",
    srcs = ["ops/op_def_library_test.py"],
    main = "ops/op_def_library_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":ops",
    ],
)

tf_gen_op_wrapper_py(
    name = "array_ops",
    hidden = [
        "BroadcastGradientArgs",
        "ConcatOffset",
        "Concat",
        "Const",
        "EditDistance",
        "MirrorPad",
        "MirrorPadGrad",
        "OneHot",
        "Pack",
        "Pad",
        "Placeholder",
        "RefIdentity",
        "Split",
        "Slice",
        "TileGrad",  # Exported through array_grad instead of array_ops.
        "ZerosLike",  # TODO(josh11b): Use this instead of the Python version.
        "Unpack",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "candidate_sampling_ops",
    hidden = [
        "AllCandidateSampler",
        "ComputeAccidentalHits",
        "FixedUnigramCandidateSampler",
        "LearnedUnigramCandidateSampler",
        "LogUniformCandidateSampler",
        "ThreadUnsafeUnigramCandidateSampler",
        "UniformCandidateSampler",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "control_flow_ops",
    hidden = [
        "Switch",
        "Merge",
        "RefMerge",
        "Exit",
        "RefExit",
    ],
    require_shape_functions = True,
    deps = [
        "//tensorflow/core:control_flow_ops_op_lib",
        "//tensorflow/core:no_op_op_lib",
    ],
)

tf_gen_op_wrapper_py(
    name = "ctc_ops",
    hidden = [
        "CTCLoss",
        "CTCGreedyDecoder",
        "CTCBeamSearchDecoder",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "data_flow_ops",
    hidden = [
        "FIFOQueue",
        "HashTable",
        "InitializeTable",
        "LookupTableFind",
        "LookupTableSize",
        "Mutex",
        "MutexAcquire",
        "MutexRelease",
        "PaddingFIFOQueue",
        "QueueClose",
        "QueueDequeue",
        "QueueDequeueMany",
        "QueueDequeueUpTo",
        "QueueEnqueue",
        "QueueEnqueueMany",
        "QueueSize",
        "RandomShuffleQueue",
        "Stack",
        "StackPop",
        "StackPush",
        "StackClose",
        "TensorArray",
        "TensorArrayClose",
        "TensorArrayConcat",
        "TensorArrayGrad",
        "TensorArrayRead",
        "TensorArrayPack",
        "TensorArraySize",
        "TensorArraySplit",
        "TensorArrayUnpack",
        "TensorArrayWrite",
        "GetSessionHandle",
        "GetSessionTensor",
        "DeleteSessionTensor",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "image_ops",
    hidden = [
        "RandomCrop",
        "ResizeBilinearGrad",
        "ResizeNearestNeighborGrad",
        "AdjustContrastv2",
        "ScaleImageGrad",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "io_ops",
    hidden = [
        "FixedLengthRecordReader",
        "IdentityReader",
        "ReaderClose",
        "ReaderEnqueueWork",
        "ReaderNumRecordsProduced",
        "ReaderNumWorkUnitsCompleted",
        "ReaderRead",
        "ReaderReadUpTo",
        "ReaderReset",
        "ReaderRestoreState",
        "ReaderSerializeState",
        "ReaderWorkQueueLength",
        "Restore",
        "RestoreSlice",
        "Save",
        "SaveSlices",
        "ShardedFilename",
        "ShardedFilespec",
        "TextLineReader",
        "TFRecordReader",
        "WholeFileReader",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "linalg_ops",
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "logging_ops",
    hidden = [
        "Assert",
        "AudioSummary",
        "HistogramAccumulatorSummary",
        "HistogramSummary",
        "ImageSummary",
        "MergeSummary",
        "Print",
        "ScalarSummary",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "math_ops",
    hidden = [
        "Abs",
        "All",
        "Any",
        "BatchMatMul",
        "Complex",
        "Max",
        "Mean",
        "Min",
        "Pow",
        "Prod",
        "Range",
        "SparseMatMul",
        "Sum",
        "MatMul",
        "Sigmoid",
        "Tanh",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "nn_ops",
    hidden = [
        "AvgPoolGrad",  # "*Grad" accessible through nn_grad instead of nn_ops.
        "BatchNormWithGlobalNormalization",
        "BatchNormWithGlobalNormalizationGrad",
        "SoftmaxCrossEntropyWithLogits",
        "SparseSoftmaxCrossEntropyWithLogits",
        "LRNGrad",
        "MaxPoolGrad",
        "MaxPoolGradWithArgmax",
        "ReluGrad",
        "Relu6Grad",
        "EluGrad",
        "SoftplusGrad",
        "SoftsignGrad",
        "TopK",
        "TopKV2",
        "BiasAdd",
        "BiasAddV1",
        "Relu6",
        "AvgPool",
        "MaxPool",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "parsing_ops",
    hidden = [
        "ParseExample",
        "ParseSingleSequenceExample",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "random_ops",
    hidden = [
        "RandomGamma",
        "RandomUniform",
        "RandomUniformInt",
        "RandomShuffle",
        "RandomStandardNormal",
        "TruncatedNormal",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "script_ops",
    hidden = [
        "PyFunc",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "state_ops",
    hidden = [
        "Variable",
        "TemporaryVariable",
        "DestroyTemporaryVariable",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "sparse_ops",
    hidden = [
        "DeserializeManySparse",
        "SerializeManySparse",
        "SerializeSparse",
        "SparseAdd",
        "SparseAddGrad",
        "SparseConcat",
        "SparseSplit",
        "SparseSelectLastK",
        "SparseReorder",
        "SparseReshape",
        "SparseToDense",
        "SparseTensorDenseAdd",
        "SparseTensorDenseMatMul",
    ],
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "string_ops",
    require_shape_functions = True,
)

tf_gen_op_wrapper_py(
    name = "user_ops",
    hidden = [
        "Fact",
    ],
    require_shape_functions = False,
)

tf_gen_op_wrapper_py(
    name = "training_ops",
    out = "training/gen_training_ops.py",
    require_shape_functions = True,
)

py_library(
    name = "ops",
    srcs = [
        "ops/array_grad.py",
        "ops/array_ops.py",
        "ops/candidate_sampling_ops.py",
        "ops/check_ops.py",
        "ops/clip_ops.py",
        "ops/control_flow_grad.py",
        "ops/control_flow_ops.py",
        "ops/data_flow_grad.py",
        "ops/data_flow_ops.py",
        "ops/embedding_ops.py",
        "ops/gen_array_ops.py",
        "ops/gen_control_flow_ops.py",
        "ops/gen_ctc_ops.py",
        "ops/gen_data_flow_ops.py",
        "ops/gen_image_ops.py",
        "ops/gen_io_ops.py",
        "ops/gen_linalg_ops.py",
        "ops/gen_logging_ops.py",
        "ops/gen_math_ops.py",
        "ops/gen_nn_ops.py",
        "ops/gen_random_ops.py",
        "ops/gen_state_ops.py",
        "ops/gen_string_ops.py",
        "ops/gradients.py",
        "ops/histogram_ops.py",
        "ops/image_grad.py",
        "ops/image_ops.py",
        "ops/init_ops.py",
        "ops/io_ops.py",
        "ops/linalg_grad.py",
        "ops/linalg_ops.py",
        "ops/logging_ops.py",
        "ops/math_grad.py",
        "ops/math_ops.py",
        "ops/nn.py",
        "ops/nn_grad.py",
        "ops/nn_ops.py",
        "ops/numerics.py",
        "ops/parsing_ops.py",
        "ops/partitioned_variables.py",
        "ops/random_ops.py",
        "ops/rnn.py",
        "ops/rnn_cell.py",
        "ops/script_ops.py",
        "ops/seq2seq.py",
        "ops/session_ops.py",
        "ops/sparse_grad.py",
        "ops/sparse_ops.py",
        "ops/special_math_ops.py",
        "ops/standard_ops.py",
        "ops/state_grad.py",
        "ops/state_ops.py",
        "ops/string_ops.py",
        "ops/template.py",
        "ops/tensor_array_grad.py",
        "ops/tensor_array_ops.py",
        "ops/variable_scope.py",
        "ops/variables.py",
        "user_ops/user_ops.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":array_ops",
        ":candidate_sampling_ops",
        ":control_flow_ops",
        ":data_flow_ops",
        ":framework",
        ":functional_ops_lib",
        ":io_ops",
        ":linalg_ops",
        ":logging_ops",
        ":math_ops",
        ":nn_ops",
        ":parsing_ops",
        ":random_ops",
        ":script_ops",
        ":sparse_ops",
        ":string_ops",
        ":user_ops",
    ],
)

py_library(
    name = "training",
    srcs = glob(
        ["training/**/*.py"],
        exclude = ["**/*test*"],
    ),
    srcs_version = "PY2AND3",
    deps = [
        ":client",
        ":framework",
        ":lib",
        ":ops",
        ":protos_all_py",
        ":pywrap_tensorflow",
        ":training_ops",
    ],
)

py_library(
    name = "client",
    srcs = glob(
        ["client/**/*.py"],
        exclude = ["**/*test*"],
    ),
    srcs_version = "PY2AND3",
    deps = [
        ":framework",
        ":ops",
        ":session",
        ":training_ops",
    ],
)

py_library(
    name = "util",
    srcs = glob(["util/**/*.py"]),
    srcs_version = "PY2AND3",
    deps = ["@protobuf//:protobuf_python"],
)

tf_proto_library(
    name = "protos_all",
    srcs = glob(
        ["**/*.proto"],
        exclude = [
            "util/protobuf/compare_test.proto",
        ],
    ),
    go_api_version = 2,
)

tf_proto_library_py(
    name = "compare_test_proto",
    testonly = 1,
    srcs = ["util/protobuf/compare_test.proto"],
)

py_test(
    name = "protobuf_compare_test",
    size = "small",
    srcs = ["util/protobuf/compare_test.py"],
    main = "util/protobuf/compare_test.py",
    srcs_version = "PY2AND3",
    deps = [
        ":compare_test_proto_py",
        ":platform_test",
        ":util",
    ],
)

py_test(
    name = "events_writer_test",
    size = "small",
    srcs = [
        "client/events_writer_test.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":framework_test_lib",
        ":lib",
        ":platform_test",
    ],
)

py_library(
    name = "device_lib",
    srcs = ["client/device_lib.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":pywrap_tensorflow",
    ],
)

cuda_py_tests(
    name = "device_lib_test",
    size = "small",
    srcs = [
        "client/device_lib_test.py",
    ],
    additional_deps = [
        ":device_lib",
    ],
)

tf_cuda_library(
    name = "tf_session_helper",
    srcs = ["client/tf_session_helper.cc"],
    hdrs = ["client/tf_session_helper.h"],
    deps = [
        ":construction_fails_op",
        ":numpy_lib",
        ":test_ops_kernels",
        "//tensorflow/core",
        "//tensorflow/core:all_kernels",
        "//tensorflow/core:direct_session",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_cc",
        "//third_party/py/numpy:headers",
        "//util/python:python_headers",
    ],
)

tf_py_wrap_cc(
    name = "pywrap_tensorflow",
    srcs = ["tensorflow.i"],
    swig_includes = [
        "client/device_lib.i",
        "client/events_writer.i",
        "client/tf_session.i",
        "framework/python_op_gen.i",
        "lib/core/py_func.i",
        "lib/core/strings.i",
        "lib/io/py_record_reader.i",
        "lib/io/py_record_writer.i",
        "platform/base.i",
        "platform/numpy.i",
        "training/saver_io.i",
        "training/server_lib.i",
        "util/port.i",
        "util/py_checkpoint_reader.i",
    ],
    deps = [
        ":numpy_lib",
        ":py_func_lib",
        ":py_record_reader_lib",
        ":py_record_writer_lib",
        ":python_op_gen",
        ":tf_session_helper",
        "//tensorflow/core/distributed_runtime/rpc:grpc_server_lib",
        "//tensorflow/core/distributed_runtime/rpc:grpc_session",
        "//tensorflow/core:lib",
        "//tensorflow/core/distributed_runtime:server_lib",
        "//util/python:python_headers",
    ] + tf_additional_lib_deps(),
)

py_library(
    name = "lib",
    srcs = glob(["lib/**/*.py"]),
    srcs_version = "PY2AND3",
    deps = [
        ":pywrap_tensorflow",
    ],
)

py_library(
    name = "session",
    srcs = ["client/session.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":framework",
        ":ops",
        ":pywrap_tensorflow",
    ],
)

py_test(
    name = "server_lib_test",
    size = "small",
    srcs = ["training/server_lib_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":extra_py_tests_deps",
        ":framework",
        ":framework_test_lib",
        ":session",
    ],
)

py_library(
    name = "timeline",
    srcs = ["client/timeline.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":platform",
    ],
)

# Just used by tests.
tf_cuda_library(
    name = "construction_fails_op",
    testonly = 1,
    srcs = ["client/test_construction_fails_op.cc"],
    deps = [
        "//tensorflow/core",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_cc",
    ],
    alwayslink = 1,
)

py_test(
    name = "session_test",
    size = "small",
    srcs = ["client/session_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":framework",
        ":framework_test_lib",
        ":session",
    ],
)

py_test(
    name = "timeline_test",
    size = "small",
    srcs = ["client/timeline_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":timeline",
        "//tensorflow:tensorflow_py",
    ],
)

py_test(
    name = "graph_util_test",
    size = "small",
    srcs = ["client/graph_util_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":framework",
        ":framework_test_lib",
        "//tensorflow:tensorflow_py",
    ],
)

py_library(
    name = "kernel_tests/gradient_checker",
    srcs = ["kernel_tests/gradient_checker.py"],
    srcs_version = "PY2AND3",
)

medium_kernel_test_list = glob([
    "kernel_tests/concat_op_test.py",
    "kernel_tests/division_future_test.py",
    "kernel_tests/fft_ops_test.py",
    "kernel_tests/pooling_ops_test.py",
    "kernel_tests/rnn_test.py",
    "kernel_tests/random_gamma_test.py",
    "kernel_tests/scatter_ops_test.py",
    "kernel_tests/seq2seq_test.py",
    "kernel_tests/slice_op_test.py",
    "kernel_tests/sparse_ops_test.py",
    "kernel_tests/sparse_matmul_op_test.py",
    "kernel_tests/sparse_tensor_dense_matmul_op_test.py",
])

sharded_kernel_test_list = glob([
    "kernel_tests/batch_matrix_band_part_op_test.py",
    "kernel_tests/cwise_ops_test.py",
    "kernel_tests/embedding_ops_test.py",
    "kernel_tests/linalg_grad_test.py",
    "kernel_tests/conv_ops_3d_test.py",
])

cpu_only_kernel_test_list = glob([
    "kernel_tests/attention_ops_test.py",
    "kernel_tests/barrier_ops_test.py",
    "kernel_tests/bcast_ops_test.py",
    "kernel_tests/benchmark_test.py",
    "kernel_tests/candidate_sampler_ops_test.py",
    "kernel_tests/cholesky_op_test.py",
    "kernel_tests/clip_ops_test.py",
    "kernel_tests/decode_csv_op_test.py",
    "kernel_tests/decode_raw_op_test.py",
    "kernel_tests/determinant_op_test.py",
    "kernel_tests/diag_op_test.py",
    "kernel_tests/edit_distance_op_test.py",
    "kernel_tests/fifo_queue_test.py",
    "kernel_tests/identity_op_py_test.py",
    "kernel_tests/in_topk_op_test.py",
    "kernel_tests/io_ops_test.py",
    "kernel_tests/listdiff_op_test.py",
    "kernel_tests/logging_ops_test.py",
    "kernel_tests/lookup_table_op_test.py",
    "kernel_tests/lrn_op_py_test.py",
    "kernel_tests/matrix_inverse_op_test.py",
    "kernel_tests/matrix_solve_op_test.py",
    "kernel_tests/matrix_triangular_solve_op_test.py",
    "kernel_tests/matrix_solve_ls_op_test.py",
    "kernel_tests/mutex_ops_test.py",
    "kernel_tests/parsing_ops_test.py",
    "kernel_tests/partitioned_variables_test.py",
    "kernel_tests/queue_ops_test.py",
    "kernel_tests/random_shuffle_queue_test.py",
    "kernel_tests/save_restore_ops_test.py",
    "kernel_tests/segment_reduction_ops_test.py",
    "kernel_tests/self_adjoint_eig_op_test.py",
    "kernel_tests/sparse_add_op_test.py",
    "kernel_tests/sparse_concat_op_test.py",
    "kernel_tests/sparse_split_op_test.py",
    "kernel_tests/sparse_reorder_op_test.py",
    "kernel_tests/sparse_reshape_op_test.py",
    "kernel_tests/sparse_to_dense_op_test.py",
    "kernel_tests/sparsemask_op_test.py",
    "kernel_tests/summary_ops_test.py",
    "kernel_tests/template_test.py",
    "kernel_tests/topk_op_test.py",
    "kernel_tests/unique_op_test.py",
    "kernel_tests/variable_scope_test.py",
    "kernel_tests/variables_test.py",
    "kernel_tests/where_op_test.py",
])

py_tests(
    name = "cpu_only_kernel_tests",
    size = "small",
    srcs = cpu_only_kernel_test_list,
)

py_tests(
    name = "reader_ops_test",
    size = "small",
    srcs = ["kernel_tests/reader_ops_test.py"],
    additional_deps = [
        ":lib",
    ],
)

cuda_py_tests(
    name = "op_tests",
    size = "small",
    srcs = glob(
        ["ops/*_test.py"],
        exclude = [
            "ops/image_ops_test.py",
            "ops/nn_batchnorm_test.py",
            "ops/nn_conv_test.py",
            "ops/nn_test.py",
            "ops/nn_xent_test.py",
            "ops/op_def_library_test.py",
        ],
    ),
)

cuda_py_tests(
    name = "medium_op_tests",
    size = "medium",
    srcs = [
        "ops/nn_batchnorm_test.py",
        "ops/nn_conv_test.py",
        "ops/nn_test.py",
        "ops/nn_xent_test.py",
    ],
)

cuda_py_tests(
    name = "kernel_tests",
    size = "small",
    srcs = glob(
        ["kernel_tests/*_test.py"],
        exclude = [
            "**/reader_ops_test.py",
        ] + cpu_only_kernel_test_list + medium_kernel_test_list + sharded_kernel_test_list,
    ),
)

cuda_py_tests(
    name = "medium_kernel_tests",
    size = "medium",
    srcs = medium_kernel_test_list,
)

cuda_py_tests(
    name = "kernel_tests_with_sharding",
    size = "medium",
    srcs = sharded_kernel_test_list,
    shard_count = 50,
)

cuda_py_tests(
    name = "image_ops_test",
    size = "small",
    srcs = [
        "ops/image_ops_test.py",
    ],
    data = [
        "//tensorflow/core:image_testdata",
    ],
    shard_count = 5,
)

cuda_py_tests(
    name = "training_tests",
    size = "small",
    srcs = glob(
        ["training/*_test.py"],
        exclude = [
            "training/input_test.py",
            "training/server_lib_test.py",
            "training/session_manager_test.py",
            "training/supervisor_test.py",
        ],
    ),
    additional_deps = [
        ":training",
    ],
)

cuda_py_test(
    name = "session_manager_test",
    size = "medium",  # TODO(irving): Can this be made small?
    srcs = ["training/session_manager_test.py"],
    additional_deps = [
        ":training",
    ],
    main = "training/session_manager_test.py",
)

py_test(
    name = "supervisor_test",
    size = "small",
    srcs = ["training/supervisor_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":extra_py_tests_deps",
        ":training",
    ],
)

py_tests(
    name = "training_tests",
    size = "small",
    srcs = glob(
        ["training/input_test.py"],
    ),
    additional_deps = [
        ":training",
    ],
)

py_library(
    name = "summary",
    srcs = glob(
        ["summary/**/*.py"],
        exclude = ["**/*test*"],
    ),
    srcs_version = "PY2AND3",
    deps = [
        ":client",
        ":framework",
        ":protos_all_py",
        ":pywrap_tensorflow",
        ":util",
    ],
)

py_tests(
    name = "summary_tests",
    size = "small",
    srcs = glob(["summary/**/*_test.py"]),
    additional_deps = [
        ":summary",
        ":training",
    ],
)

py_library(
    name = "docs",
    srcs = [
        "framework/docs.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":platform",
    ],
)

py_binary(
    name = "gen_docs_combined",
    srcs = [
        "framework/gen_docs_combined.py",
    ],
    main = "framework/gen_docs_combined.py",
    srcs_version = "PY2AND3",
    deps = [
        ":docs",
        "//tensorflow:tensorflow_py",
    ],
)

filegroup(
    name = "all_files",
    srcs = glob(
        ["**/*"],
        exclude = [
            "**/METADATA",
            "**/OWNERS",
        ],
    ),
    visibility = ["//tensorflow:__subpackages__"],
)

cuda_py_test(
    name = "batch_norm_benchmark",
    srcs = [
        "ops/batch_norm_benchmark.py",
    ],
    main = "ops/batch_norm_benchmark.py",
)
