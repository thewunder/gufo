# gufo

Arch package for [Gufo](https://github.com/gufo-org/gufo) 0.5.0, the inference
engine for AMD Strix Halo (`gfx1151`).

Kernels are built only for gfx1151. This package will not run on other AMD GPUs.
Model weights are not included.

## Build

```sh
makepkg -si
```

Runtime libraries (`hip-runtime-amd`, `hipblas`, `hipblaslt`, `rocblas`) and
the header-only ROCm pieces (`hipcub`, `rocprim`, `rocwmma`) come from Arch
`extra`. `ffmpeg` is optional and is used only for video and audio output.

After install, your user needs the `render` and `video` groups:

```sh
sudo usermod -aG render,video "$USER"
gufo diagnose
gufo serve llm --model /path/to/model.gguf
```

`gufo-server` is installed as a symlink to `gufo`.

## AUR

This tree is AUR-shaped (`PKGBUILD`, `.SRCINFO`, `.nvchecker.toml`) but is not
submitted to the AUR. To publish it, adopt `gufo` on the AUR and push:

```sh
git remote add aur ssh://aur@aur.archlinux.org/gufo.git
git push aur main
```
