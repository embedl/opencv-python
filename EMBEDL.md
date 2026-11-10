# Embedl's opencv-python build

This fork builds `opencv-python-headless` for Linux x86_64 and aarch64 with FFmpeg 9.0.2 instead of the FFmpeg the PyPI wheel bundles. Embedl's model images install it in place of the PyPI wheel. Owner: embedl-axel. Background: embedl/model-meta#7.

## What differs from upstream

The branch `embedl/5.0.0-ffmpeg9` starts at upstream's tag `93` (opencv-python 5.0.0.93, OpenCV 5.0.0) and changes:

- `patches/patchFFmpeg9`: OpenCV's port of videoio to FFmpeg 9 (opencv/opencv#30513, commit eb604b9a2b on 5.x), applied by `setup.py` to the `opencv` submodule for Linux wheels.
- `docker/manylinux_2_28/Dockerfile_*`: FFmpeg 9.0.2, checked against its SHA-256 (the tarball's signature by FFmpeg's release key FCF9 86EA 15E6 E293 A564 4F10 B432 2F04 D676 58D8 was verified when the hash was recorded), and the GitHub-hosted runner's UID on aarch64.
- `.github/workflows/embedl_wheels.yml` replaces upstream's workflows, which need OpenCV's own runners and images. It builds the builder image in the run, builds the headless abi3 wheel per architecture, checks that the bundled libavutil reports FFmpeg 9.0.2 and that a video round trip works, runs OpenCV's Python tests, and on a tag publishes a release.

Nothing else changes: same OpenCV source, same build options, same bundled libraries apart from FFmpeg.

## Releases

A tag `93+embedl.N` on this branch builds version `5.0.0.93+embedl.N` and publishes a GitHub release with:

- the two wheels,
- `ffmpeg-9.0.2.tar.gz`, the source of the FFmpeg the wheels bundle (LGPL-2.1-or-later; the patch above is the only change to OpenCV, whose licence is Apache-2.0),
- `SHA256SUMS`.

Install a wheel by its release URL with its hash, for example in a requirements file:

```
opencv-python-headless @ https://github.com/embedl/opencv-python/releases/download/93%2Bembedl.1/opencv_python_headless-5.0.0.93%2Bembedl.1-cp37-abi3-manylinux_2_28_x86_64.whl --hash=sha256:<from SHA256SUMS>
```

## Moving to a newer upstream

When upstream releases an opencv-python whose OpenCV and FFmpeg carry these fixes, the model images move back to the PyPI wheel and this branch stops being built.
