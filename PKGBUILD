# Maintainer: Michael O'Connell <wundbread@gmail.com>

pkgname=gufo
pkgver=0.5.0
pkgrel=1
pkgdesc='Inference engine for AMD Strix Halo (gfx1151)'
arch=('x86_64')
url='https://github.com/gufo-org/gufo'
license=('Apache-2.0' 'BSD-3-Clause' 'MIT')
depends=(
  'curl'
  'gcc-libs'
  'glibc'
  'hip-runtime-amd'
  'hipblas'
  'hipblaslt'
  'icu'
  'libjpeg-turbo'
  'libpng'
  'libwebp'
  'openmp'
  'openssl'
  'rocblas'
)
makedepends=(
  'cmake'
  'hipcub'
  'ninja'
  'pkgconf'
  'rocprim'
  'rocwmma'
  'rocm-llvm'
)
optdepends=(
  'ffmpeg: video and audio output via ffmpeg and ffprobe'
)
install=gufo.install
# HIP does not support LTO. RelWithDebInfo matches upstream's release preset
# (-O2 -g -DNDEBUG) and does not inject CMake's Release -O3.
options=('!lto')
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/gufo-org/gufo/archive/refs/tags/v${pkgver}.tar.gz")
sha256sums=('403018fb390535a22ee7c387575e8df8d856cbb80b47740dbbea3588020e56ec')

build() {
  # Arch's ROCm LLVM lives under /opt/rocm/lib/llvm, not the upstream
  # /opt/rocm/llvm layout. hipconfig -l prints that bin directory.
  local hipcxx
  hipcxx="$(hipconfig -l)/clang++"
  if [[ ! -x "${hipcxx}" ]]; then
    echo "HIP compiler not found at ${hipcxx} (rocm-llvm)" >&2
    exit 1
  fi

  # HIP rejects CET and can crash when libstdc++ assertions are enabled.
  export CFLAGS="${CFLAGS} -fcf-protection=none"
  export CXXFLAGS="${CXXFLAGS//-Wp,-D_GLIBCXX_ASSERTIONS/} -fcf-protection=none"
  export HIP_PLATFORM=amd
  export ROCM_PATH=/opt/rocm
  export HIP_PATH=/opt/rocm

  local cmake_options=(
    -B build
    -S "${pkgname}-${pkgver}"
    -G Ninja
    -W no-dev
    -D CMAKE_BUILD_TYPE=RelWithDebInfo
    -D CMAKE_INSTALL_PREFIX=/usr
    -D CMAKE_PREFIX_PATH=/opt/rocm
    -D CMAKE_HIP_COMPILER="${hipcxx}"
    -D CMAKE_HIP_ARCHITECTURES=gfx1151
    -D CMAKE_HIP_FLAGS=-fcf-protection=none
    -D BUILD_TESTING=OFF
    -D ENGINE_ENABLE_HIP=ON
    -D GUFO_BUILD_TOOLS=OFF
    -D GUFO_RELEASE_VERSION="${pkgver}"
    -D GUFO_REVISION="v${pkgver}"
  )

  cmake "${cmake_options[@]}"
  cmake --build build
}

package() {
  DESTDIR="${pkgdir}" cmake --install build
}
