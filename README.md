Wayfire Benchmarking & Scene Graph Inspection
================================================================

This updated and expanded tutorial will guide you through setting up and using the `ammen99-bench` and `ammen99-debugging` plugins for benchmarking, scene graph inspection, and debugging in Wayfire — a powerful Wayland compositor.

📦 Step 1: Clone the Plugins Repository
---------------------------------------

    git clone https://github.com/ammen99/wayfire-plugins 
    cd wayfire-plugins

🔧 Step 2: Build and Install Plugins
------------------------------------

    meson setup build --prefix=/usr
    ninja -C build
    sudo ninja -C build install

> This installs both `ammen99-bench` and `ammen99-debugging` plugins.

📄 Step 3: Configure Wayfire (`ammen.ini`) and Create `ammen.py` Stress Script
------------------------------------
save the following to /path/to/ammen.py
    import subprocess
    
    # Start multiple instances of weston-simple-egl to stress the GPU
    for i in range(50):
        subprocess.Popen(["/usr/bin/weston-simple-egl", "-b", "-f"])

Edit or create your config file:

    [output:HEADLESS-1]
    mode = 3840x2160@1000000
    
    [core]
    plugins = ammen99-bench ammen99-debugging autostart ipc ipc-rules expo stipc scale
    
    [autostart]
    ammen = python /path/to/ammen.py
    
    [ammen99-bench]
    average_frames = 1.000000
    refresh_interval = 1.000000
    stdout = true
    immediate_draw = true

⚙️ Step 4: Launch Wayfire with Environment Variables
----------------------------------------------------

    WLR_RENDERER=vulkan \
    WLR_RENDER_DRM_DEVICE=/dev/dri/renderD128 \
    WLR_BACKENDS=headless \
    wayfire -c /path/to/ammen.ini
    
📊 Expected Output During Benchmarking
--------------------------------------

- II 14-06-25 15:03:16.306 - [src/bench.cpp:99] Running at 828 fps
- II 14-06-25 15:03:17.307 - [src/bench.cpp:99] Running at 830 fps
- II 14-06-25 15:03:18.307 - [src/bench.cpp:99] Running at 830 fps
- II 14-06-25 15:03:19.308 - [src/bench.cpp:99] Running at 834 fps
- II 14-06-25 15:03:20.308 - [src/bench.cpp:99] Running at 833 fps
  
- You can connect the benchmark wayfire instance using `socket.connect(/run/user/1000/wayfire-wayland-2-.socket)` and test some plugins FPS with IPC actions.

### inspect internal structures with `ammen99-debugging`

*   Adds utility functions like `wdbg.py` to inspect internal structures.
*   Enables real-time access to the scene graph, useful for debugging layout and rendering issues.

🧪 Use `wdbg.py` to Inspect Scene Graph
-----------------------------------------------

In the `scripts/` directory, run:

    python wdbg.py dump-scenegraph

### Example Output:

    root () id=0x5567cc9170b0 geometry=(0,0 5930x1080)
    |-layer_dwidget () id=0x5567cc932ba0 geometry=(0,0 2560x0)
    | |-output DP-1 () id=0x5567ccc4f640 geometry=(2560,0 0x0)
    | |-output DP-2 () id=0x5567ccc547a0 geometry=(0,0 0x0)
    |-layer_lock () id=0x5567cc9370d0 geometry=(0,0 2560x0)
    | |-output DP-1 () id=0x5567ccc4f400 geometry=(2560,0 0x0)
    | |-output DP-2 () id=0x5567ccc54200 geometry=(0,0 0x0)
    |-layer_overlay () id=0x5567cc93f1b0 geometry=(0,0 2560x0)
    | |-output DP-1 () id=0x5567ccc4f1c0 geometry=(2560,0 0x0)
    | |-output DP-2 () id=0x5567ccc4c9e0 geometry=(0,0 0x0)
    |-layer_unmanaged () id=0x5567cc76bca0 geometry=(0,0 2560x0)
    | |-output DP-1 () id=0x5567ccc4ef80 geometry=(2560,0 0x0)
    | |-output DP-2 () id=0x5567ccc4c440 geometry=(0,0 0x0)
    |-layer_top () id=0x5567cc90a800 geometry=(0,0 4480x42)

📌 How It Works Internally
--------------------------

The `dump-scenegraph` command works by calling `wf::dump_scene()` from inside the Wayfire codebase.

You can add temporary calls to `wf::dump_scene()` at key points during rendering or event handling to inspect the state of the scene tree dynamically.

### Example usage

    #include 
    ...
    wf::dump_scene();

✅ Summary
---------

You've now set up:

*   A custom Wayfire environment using plugins from [ammen99/wayfire-plugins](https://github.com/ammen99/wayfire-plugins).
*   Real-time benchmarking via `ammen99-bench`.
*   Scene graph debugging with `ammen99-debugging` and `wdbg.py`.
*   GPU stress testing using `weston-simple-egl`.


🧑‍💻 Author
------------

Built based on work from [ammen99/wayfire-plugins](https://github.com/ammen99/wayfire-plugins)
