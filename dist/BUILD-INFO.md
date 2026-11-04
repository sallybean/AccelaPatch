# ACCELA baseline x86-64 rebuild

- Artifact: `ACCELA-baseline-x86_64.AppImage`
- Build date: 2026-11-03
- Base AppImage SHA-256: `66eec44b479c1c889411c1d9259f9f04da97225cfd04206d5a09af06177a7926`
- Output AppImage SHA-256: `a779e2a87ff44da703ea6c8faff7b4193c4f6b4dc5d6b0b8c87bffdda030b03f`
- Rebuilt `_ma_playback.abi3.so` SHA-256: `1b5c3759ceef0f7a06f0a400dbf1d3c8685a9330db33c0dc098879fe17463601`
- Source: [just_playback 0.1.8 on PyPI](https://pypi.org/project/just_playback/0.1.8/)
- Source archive SHA-256: `e5974749f10faae7bb76b3f1e3779a1369275ab179d8759dae27e9f29b410350`

The source's `build_ffi_module.py` adds `-march=native` on non-macOS builds. For this rebuild that target was changed to `-march=x86-64 -mtune=generic -mno-avx -mno-avx2`. It used the AppImage's bundled Python 3.13.0/CFFI toolchain with GCC 13.3.0, and updated the just_playback wheel `RECORD` hash for the replacement extension.

## Verification

- The rebuilt extension imports in the bundled Python and completes miniaudio context initialization and playback-device enumeration on the Pentium Gold 5405U (`ma_result=0`).
- `objdump` inspection found no AVX/AVX2/AVX-512 mnemonics or YMM/ZMM/K-register operands in the rebuilt extension.
- A WAV playback check loaded a 0.5-second file, started playback, and stopped cleanly.
- The repacked AppImage passed its embedded SquashFS and AppImage-offset checks. An offscreen launch logged `Main window displayed successfully` and ran until the 25-second smoke-test timeout, without SIGILL.

The smoke-test container had no reachable X11 display and no usable ALSA/JACK output device. The offscreen launch validates application initialization; the playback check validates the stream calls and state transitions, but neither confirms audible output from physical speakers. The instruction audit covered `_ma_playback.abi3.so`, not every native ELF in the AppImage.
