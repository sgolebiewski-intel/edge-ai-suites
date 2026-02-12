# Smart Tolling Application

The **Metro Smart Tolling Application** is a high-precision Edge AI solution designed to revolutionize automated tolling. By fusing multi-camera inputs (Front, Rear, and Side profiles), the system delivers accurate vehicle detection and classification, license plate detection, color classification, axle counting and tariffing.

Enabling such use cases across multiple viewpoints helps in understanding the object interaction with the real world in 3-D space. All the components used run on a single system enabling low latency, simplified deployment and cost efficiency.

## Key Features



- **Vehicle Axle Detection**:

  Determines vehicle class based on axle and wheel count
  Intended for toll classification and revenue calculation.
  Revenue Protection**: Advanced "Lift Axle" detection using Computer Vision allows for accurate tariffing.

- **License Plate Detection**:

  Identifies vehicles uniquely using license plates
  Intended for reading vehicle license plate text from both front and rear views.

- **Visualization & Analytics**:

  Provides real-time and historical insights for toll operators.

- **Accuracy**: Multi view accuracy.



### Performance & Optimization

The system achieves high-throughput processing on Edge hardware through specific optimizations defined in `config.json`.
The `docker-compose.yml` file mentions all the services and the pipelines are configured in `config.json` file.

### 2.1 Zero-Copy Video Pipeline

Unlike standard OpenCV pipelines that copy frames to CPU RAM, this solution utilizes **VASurface Sharing plugin**.

- **Mechanism:** Decoded video frames remain in GPU memory (`video/x-raw(memory:VAMemory)`).
- **Benefit:** Zero-copy inference eliminates PCIe bandwidth bottlenecks, reducing end-to-end latency by ~40%.
- **Config Evidence:** `pre-process-backend=va-surface-sharing` used in all `gvadetect` elements.

### 2.2 Dynamic ROI Inference (Hierarchical Execution)

To maximize efficiency, heavy neural networks (like Axle Counting) do not run on the full 4K frame.

- **Logic:** The "Vehicle Type" model runs first to find the bounding box.
- **Optimization:** The Axle model is configured with `inference-region=roi-list`,
  forcing it to execute *only* within the coordinates of the detected vehicle.
- **Impact:** Reduces pixel processing load by >80% for sparse traffic scenes.

### 2.3 Hybrid Workload Distribution

The pipeline intelligently maps models to available accelerators to prevent resource contention:

- **GPU (Flex Series):** Handles heavy convolution tasks (Vehicle Detection, LPR, Axle Counting).
- **CPU (Xeon):** Handles lighter classification tasks (Vehicle Color) and post-processing adapters (`gvapython`).



## Key Benefits


- **Revenue Protection**: Advanced "Lift Axle" detection using Computer Vision allows for accurate tariffing.
- **Audit Compliance**: Every transaction includes an "Image Evidence" for simplified auditing.

## How it Works

The system uses the **Metro Edge Architecture** based on three key principles:

1. **Perception**: Deep Learning Streamer (DLStreamer) processes 3/4 camera feeds.
2. **Control**: SceneScape Controller aggregates metadata.
3. **Analytics**: Node-RED transforms events into traffic insights (Traffic Volume, Flow Efficiency, Tariffing).

For more details, refer to [How it Works](./how-it-works.md).

## Learn More

- [System Requirements](./get-started/system-requirements.md): Hardware, OS and Software Prerequisites.
- [Get Started](./get-started.md): Installation, Configuration and Launch steps.
- [How it works](./how-it-works.md): Engineering Specs, Zero-Copy Pipeline and API usage.
- [Troubleshooting](./troubleshooting.md): Solutions to common issues.

<!--hide_directive
:::{toctree}
:hidden:

get-started
how-it-works
troubleshooting

:::
hide_directive-->
