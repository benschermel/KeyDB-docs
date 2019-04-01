# Docker Image to Enable Flash Storage

See [wiki](https://github.com/JohnSully/KeyDB/wiki/FLASH-Storage) for detailed explanation

This image has additional dependencies required to run binaries. You must use the binaries compiled with 'make MALLOC=memkind'. Binaries can be retrieved with:
```
$ docker run -it --rm -v /path-to-dump-binaries:/keydb_bin eqalpha/keydb-build-bin:flash
```
Note these binaries are pulled from unstable branch, we use the binaries from latest release. These binaries to be copied to docker image. Currently supported for x86_64 (amd64). For useage:
```
docker run -it --name mykeydb --mount type=bind,target=/tmp/keydbflash,source=/path-to-btrfs-ssd-mount-location-you-made/ eqalpha/keydb:flash
```
for a detailed walthrough check out the example in Docker_Examples