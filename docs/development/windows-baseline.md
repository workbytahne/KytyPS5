# Windows build and test baseline — research track

Status: **Not yet executed or verified locally**  
Target: Windows x64, source revision `bb98f26433fa9e711c219aac1711fe8ecba5bf3a` (initial `research/baseline` branch point, 2026-10-07)  
Owner: PS5 Emulator Build project  
Purpose: obtain a reproducible baseline **before** changing runtime, GPU, shaders, or game compatibility.

## What is known

- `research/baseline` initially matched `main` at `bb98f26433fa9e711c219aac1711fe8ecba5bf3a`.
- `.github/workflows/build.yml` defines a Windows 2022 job with `clang-cl`, CMake, Ninja, Qt 6, and glslang. It builds `launcher` and regression targets, runs selected CTest cases, and installs a launchable artifact.
- The repository's README documents build prerequisites and commands.
- **No successful Windows build, test, launcher smoke test, or GTA 6 execution was independently verified by this research note.**

## Milestone 1 acceptance criteria

- [ ] Record Windows version, CPU, RAM, GPU, Vulkan driver, Git, CMake, Ninja, `clang-cl`, glslang, and Qt versions.
- [ ] Fetch all submodules recursively.
- [ ] Configure clean Release build with the supported clang-cl toolchain.
- [ ] Build launcher and existing Windows CI regression targets.
- [ ] Run the Windows CI `ctest` selection, preserving logs and counts of passing, failing, and skipped tests.
- [ ] Stage the install and verify launcher starts **without loading any game**, if the machine permits.
- [ ] Record precise SHA, command lines, failures, mitigations, and next smallest fix.
- [ ] File any source modifications as a separate, reviewable PR (do not alter `main` directly).

## Windows command sequence

Run in **x64 Native Tools Command Prompt for Visual Studio 2022** from the repository root. Before running the commands, install the requirements in the README: Visual Studio C++ workload with Clang tools, Git, CMake, Ninja, Qt 6 MSVC2022 x64 (Concurrent/Network/Widgets), and glslang on PATH.

```cmd
git rev-parse HEAD
git submodule update --init --recursive
git --version
cmake --version
ninja --version
clang-cl --version
glslangValidator --version
```

Substitute the actual Qt installation path:

```cmd
cmake -S . -B _Build/windows -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=clang-cl -DCMAKE_CXX_COMPILER=clang-cl -DCMAKE_PREFIX_PATH="C:/Qt/6.x.x/msvc2022_64"
cmake --build _Build/windows --target launcher audio_out2_port_tests pad_haptics_tests controller_settings_tests ime_dialog_tests lru_cache_tests virtual_memory_allocation_tests shader_recompiler_compute_tests resource_tracking_tests scalar_provenance_tests resource_materialization_tests shader_vertex_metadata_tests archive_file_tests avplayer_file_tests --parallel
ctest --test-dir _Build/windows --output-on-failure -R "^(audio_out2_port|pad_haptics|controller_settings|ime_dialog|lru_cache|resource_tracking|scalar_provenance|resource_materialization|shader_vertex_metadata|archive_file|avplayer_file|virtual_memory_allocation)$"
cmake --install _Build/windows --prefix _Build/windows/install
```

**Caution:** The above are reproducibility instructions, not commands claimed to have been run. CTest's regex follows the existing Windows CI workflow; it is not necessarily every test. Tests build and running games are separate milestones.

## Results record (fill in only from executed evidence)

| Check | Status | Evidence / log |
|---|---|---|
| Environment/toolchain inventory | Not run | |
| Submodules | Not run | |
| CMake configure | Not run | |
| Launcher compile | Not run | |
| Test target compile | Not run | |
| Selected CTest suite | Not run | |
| Installer/staging | Not run | |
| Launcher no-game smoke test | Not run | |
| Game compatibility, including GTA 6 | Not evaluated | |

## Codex assignment (reasoning: High)

1. Inspect the current branch, repository README, `CMakeLists.txt`, and Windows CI workflow. Do **not** assume they are unchanged since this note.
2. Detect whether you have a real Windows build/test execution environment. If not, say **not run**, and inspect the GitHub Actions status after a PR triggers CI; don't fabricate output.
3. Identify the smallest blocker to a reproducible Windows build. If needed, propose a narrowly scoped fix with regression coverage.
4. Prefer separate reviewable changes. Do not import AnyPS5 code or make speculative GTA 6 compatibility claims.
5. Report evidence: commit SHA; commands executed; test counts; error excerpts; reproducibility instructions; one recommended next step.

## Safety / provenance

Do not include proprietary PS5 firmware, copyrighted game assets, encryption keys, circumvention instructions, or third-party materials without suitable rights. Use only legally obtained materials. This project is not affiliated with Sony or Rockstar.

The purpose of this track is to establish engineering reliability; **GTA 6 compatibility has not been demonstrated and no completion date can be guaranteed.**
