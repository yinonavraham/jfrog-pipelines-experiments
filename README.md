# pipetest1

yinonatest1.jfrog.io

Test implementing a JFrog Platform webhook handler using JFrog Pipelines.

On artifact deployed - trigger a webhook which calls an incoming webhook, which then triggers a pipeline that adds a property on the uploaded file.
