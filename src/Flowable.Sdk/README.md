# Flowable.Sdk

## Known Issues

[WARNING] `flowable-swagger-external-worker.nswag` is different from original/official version. Original version specifies `object` as expected respone from flowable. But, in this case, it causes generated client to throw an error on Deserialization. This is, also, might be regression issue in nswag. Visual Studio 2019 generates different processing of `HttpStatusCode.NoContent` for same specification. (NSwag toolchain v13.0.5.0).

[WARNING] `flowable-swagger-cmmn.yaml` is different from original/official version.
[WARNING] `flowable-swagger-process.yaml` is different from original/official version.

* `CreateCaseInstanceAsync` was adjusted.
