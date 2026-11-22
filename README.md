# MewUI Designer for OpenDevelop

Visual designer for [Aprillz.MewUI](https://github.com/Aprillz/MewUI) (`.mxaml`) applications,
shipped as an add-in for [OpenDevelop](https://github.com/lextudio/OpenDevelop).

## Layout

```
src/
  Libraries/MewUI.Xaml/          # MXAML document model + strict-C# generator (dependency-free)
  Tools/MewUIGen/                # MXAML -> C# code generator (CLI)
  OpenDevelop.AddIn/
    MewUIDesigner/               # In-process add-in (ICSharpCode.MewUIDesigner)
    MewUIDesigner.Host/          # Out-of-process design host
    MewUIDesigner.IntegrationTests/
tests/fixtures/MewUIFixture/      # Sample MewUI project used by integration tests
```

## Build

Requires a sibling OpenDevelop checkout at `../OpenDevelop` (or set `OPENDEVELOP_ROOT`).

```bash
export OPENDEVELOP_ROOT=/path/to/OpenDevelop
cd src/OpenDevelop.AddIn/MewUIDesigner
dotnet build -c Debug -p:OpenDevelopRoot="$OPENDEVELOP_ROOT" -p:RestoreIgnoreFailedSources=true
```

The add-in deploys into `$OPENDEVELOP_ROOT/AddIns/DisplayBindings/MewUIDesigner/`.

## Run integration tests

Set `OPENDEVELOP_APP_PATH` to an OpenDevelop binary (source build or installed app) and run:

```bash
cd src/OpenDevelop.AddIn/MewUIDesigner.IntegrationTests
OPENDEVELOP_APP_PATH=/path/to/OpenDevelop \
  dotnet run -c Debug -p:OpenDevelopRoot="$OPENDEVELOP_ROOT" -- \
  -class OpenDevelop.IntegrationTests.MewUIDesignerTests -parallel none
```

## License

MIT — see [LICENSE](LICENSE).

## CI stability notes

The integration workflow targets the latest stable OpenDevelop release and runs the
same add-in package on Windows x64/ARM64, Linux x64/ARM64 and macOS ARM64.

- The add-in and its host are AnyCPU. The host references every MewUI platform backend, and
  `MewUIRenderer` selects one at run time; Linux therefore stays model-only (the render-bound
  journeys skip there).
- Linux installs OpenDevelop's own `install-linux-prerequisites.sh` (Xvfb, Vulkan loader and
  Mesa lavapipe), because the hosted runners have no GPU and wgpu aborts without a software
  Vulkan driver.
- Windows runs OpenDevelop's `prepare-windows-desktop.ps1` so first-logon/privacy UI and the
  ARM64 WSL update prompt cannot steal the foreground from synthetic pointer input.
- A transient cold-host failure in a DevFlow action is returned as a failure instead of reaching
  the UI dispatcher (which would close the IDE); the list-edit journey retries the host call.
