# OpenSees Container

## What is this?

This container lets you run OpenSees (finite element analysis software) without installing dependencies or compiling on your machine. It's like a pre-configured environment that's isolated from your system.

## Prerequisites

- [Podman](https://podman.io/) (or Docker)
- Ubuntu 22.04 or compatible Linux distribution

## Build the Container

```bash
cd /path/to/OpenSees
podman build -t opensees -f CONTAINERS/Dockerfile .
```

This will:
- Download the OpenSees source code
- Install dependencies
- Compile OpenSees
- Create a user named `user`

The build process may take several minutes. You only need to do this once.

### Where is the image stored?

After building, the image lives in Podman's internal storage at `/var/lib/containers/storage/`. It is **not** a single file — it is managed by Podman's storage driver as multiple layers and metadata files.

### Exporting the image as a file

To create a portable `.tar` archive of the image:

```bash
podman save -o CONTAINERS/opensees.tar opensees
```

This saves the image (including all layers) to `CONTAINERS/opensees.tar`. You can then:
- Copy it to another machine
- Share it via USB, email, network transfer, etc.

To check the size of the exported image:
```bash
ls -lh CONTAINERS/opensees.tar
```

### Loading the image from a file

On a different machine (or after a clean install), load the image back into Podman:

```bash
podman load -i CONTAINERS/opensees.tar
```

This restores the `opensees` image so it can be used with `podman run` just like after a build.

Note: If you copy the `.tar` file to another machine, you do **not** need to run `podman build` again — the image is already built and packaged in the archive.

## Run the Container - Quick Start

### Interactive mode (for testing)

```bash
podman run -it opensees
```

This opens the OpenSees TCL interpreter directly. You'll see a `OpenSees>` prompt where you can type commands one by one.

To exit, type `exit` or press `Ctrl+D`.

## Running Your Own Models

### If your model is in a single file

If your analysis is in a single TCL file (e.g., `model.tcl`), run it like this:

```bash
podman run -v /path/to/model.tcl:/home/user/model.tcl:ro opensees tclsh8.6 /home/user/model.tcl
```

- `-v /path/to/model.tcl:/home/user/model.tcl:ro` - mounts your file as read-only
- `tclsh8.6 /home/user/model.tcl` - tells the container to run your file

### If your model uses multiple files

OpenSees models often split into several files:
- `model.tcl` - main script
- `nodes.tcl` - nodal coordinates
- `elements.tcl` - element definitions
- `material.tcl` - material properties
- `load.tcl` - applied loads

To run this, put all files in a folder on your computer, then:

```bash
podman run -it -v /home/you/myanalysis:/home/user/data opensees tclsh8.6 /home/user/data/model.tcl
```

Inside your `model.tcl`, make sure file paths are relative to where you mount the folder, or use the full path `/home/user/data/`.

**Important**: When sourcing other files in your TCL script (like `source nodes.tcl`), either:
1. Use absolute paths: `source /home/user/data/nodes.tcl`
2. Or change directory first in your script: `cd /home/user/data; source nodes.tcl`

### Example folder structure

On your computer:
```
/home/you/myanalysis/
├── model.tcl      # main file that sources the others
├── nodes.tcl      # node definitions
├── elements.tcl   # element definitions
└── results.tcl    # output commands
```

Your `model.tcl` should contain:
```tcl
cd /home/user/data
source nodes.tcl
source elements.tcl
# ... rest of your analysis
```

Then run:
```bash
podman run -it -v /home/you/myanalysis:/home/user/data opensees tclsh8.6 /home/user/data/model.tcl
```

## Volume Mounting Explained

The `-v` flag maps a folder from your computer into the container:

```
-v /your/local/folder:/container/folder
```

- **Left side** (`/your/local/folder`): folder on your actual computer
- **Right side** (`/container/folder`): where it appears inside the container
- `:ro` means read-only (recommended for input files)

## Container Shell Access

If you need to explore inside the container or debug issues:

```bash
podman run -it --entrypoint /bin/bash opensees
```

This gives you a terminal prompt inside the container. Type `exit` to leave.

## Useful Paths Inside Container

- Your mounted files: `/home/user/data`
- OpenSees source: `/home/user/OpenSees`
- Executable: `/home/user/bin/OpenSees`
- Libraries: `/home/user/lib`
- User home: `/home/user`

## Environment Variables

The container sets:
- `HOME=/home/user`
- `PATH=$HOME/bin:$PATH`

This means when you type `OpenSees` in the container, it finds the executable.

## Common Issues

### "File not found" errors

Check that:
1. You're mounting the correct folder
2. Your TCL scripts use the right paths (use absolute paths `/home/user/data/...` if unsure)

### Out of memory during build

If the build fails, increase available RAM or edit the Makefile to use fewer parallel jobs.

### Running in background (detached mode)

To run without keeping the terminal open:
```bash
podman run -d -v /path/to/model.tcl:/home/user/model.tcl:ro opensees tclsh8.6 /home/user/model.tcl
```

To see the output later:
```bash
podman logs <container_id>
```

## Notes

- The container uses TCL as the interpreter (as per the build instructions)
- The default user is `user` (not root) for security
- Port mounting is not needed unless running a server component
- You need to rebuild the container only if you change the Dockerfile