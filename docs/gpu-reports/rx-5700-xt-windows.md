# Radeon RX 5700 XT on Windows 11

Four unfiltered runs of the Project ReCity synthetic GPU suite on 2026-11-24. Each run translated and validated all 102 shaders. The same eight capability checks were skipped every time. Five distinct test cases failed once each and passed on the other run in the same mode. The cause of this variation is undetermined.

| RECITY_W64_EMULATE | Run | Passed / 101 | Skipped | Failed | Failing runner entries |
|---|---:|---:|---:|---:|---|
| 0 (native) | 1 | 90 | 8 | 3 | `cs_smem_soffset_imm`, `cs_w32_lds_u8_d16`, `ps_texture_sample` |
| 0 (native) | 2 | 92 | 8 | 1 | `cs_wm_bfm64` |
| 1 (emulated) | 1 | 92 | 8 | 1 | `cs_cvt_f32_sat` |
| 1 (emulated) | 2 | 93 | 8 | 0 | — |

The native repeat reported that it could not confirm a 64-wide subgroup for `cs_wm_bfm64`. The emulated first run reported a mismatch for one of 16 conversion inputs. The output does not establish a driver or translator defect.

**Skipped on all runs**

- Mesh shaders unavailable: `ms_clip`, `ms_tri`, `ms_v5_workgroup`, `ms_w64x_ballot`, `ms_wave_info_s3`.
- Fragment shader barycentrics unavailable: `ps_bary`, `ps_scc_mask32`, `ps_uniform_branch`.

**Environment and reproduction**

- AMD Radeon RX 5700 XT (1002:731f), AMD 26.11.2, Vulkan 1.4.315, compute subgroup range 32–64. Windows 11 25H2 build 26200.9974.
- Source and runner commit `cb4840b6a1d0de654ae7126f1528c0998a428984`. Windows binaries from [upstream CI run 44986886273](https://github.com/pixl27/ProjectReCity/actions/runs/44986886273), commit `02c6c858921da8204bb46134f5224e8dc65ab9b4`. Only documentation changed between these commits.
- Python 3.12.14, LLVM 23.1.6 (AMDGPU), SPIRV-Tools 2026.4. Synthetic fixtures shipped in the repository. AI-assisted execution and reporting (Codex).
- Set `RECITY_W64_EMULATE` to the value in the table. From the repository root run `python -I scripts/run_gpu_tests.py --work <fresh-directory>` with the executables in `runtime/build`. Execution order was native, emulated, native, emulated. Runner exit codes were 1, 1, 1, 0 respectively.
