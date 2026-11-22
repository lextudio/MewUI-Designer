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
