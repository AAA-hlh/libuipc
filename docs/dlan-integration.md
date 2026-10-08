# Denglin DLAN integration

`UIPC_MUDA_USE_DLAN=ON` selects the DLAN SDK while using the existing CoreX-compatible CUDA source path. It enables FP32 and disables async allocation. NVIDIA defaults are unchanged.

The embedding NovaPhy build supplies its DLAN compiler wrapper, CUDA architecture compatibility value `70`, C++20 flags, and vcpkg dependencies. Set `UIPC_DLAN_SDK_ROOT` (or `DLAN_SDK_ROOT`) to the SDK installation; the default is `/usr/local/dlgpu/sdk`. Runtime and math libraries are imported from that SDK. Parent projects keep ownership of their vcpkg manifest and install directory.

SDK compatibility changes cover the CUB attribute probe, depth-one pitched memory copies, unavailable optional sparse descriptors/handles, older Clang template parsing and structured-binding captures. DLAN follows the upstream CoreX GIPC contact/linear-system path, with synchronous allocation for the Denglin SDK. The integration uses iterative PCG; optional cuSolverSP/direct sparse APIs are not enabled. `UIPC_WITH_URDF_SUPPORT=OFF` allows an embedding application with its own importer to omit libuipc's URDF component.

The physics corrections preserve relative PCG residual stopping for small nonzero right-hand sides, reject nonfinite LDLT pivots, and use fixed-order block reduction on DLAN. The upstream GIPC contact energy, gradient and Hessian path is retained.

Validation uses NovaPhy's IPC integration tests on KS38 hardware: free fall, ground contact, initial linear/angular velocities, and known-force acceleration at 0.01, 1 and 1000 kg. See the accompanying NovaPhy PR for the source tests and complete results. This port does not establish correctness for every deformable scene, numerical scale or optional libuipc subsystem.
