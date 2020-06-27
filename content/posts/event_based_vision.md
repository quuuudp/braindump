+++
title = "Event-based Vision"
author = ["Jethro Kuan"]
lastmod = 2020-06-26T16:35:17+08:00
slug = "event_based_vision"
draft = false
+++

Event-based cameras are bio-inspired sensors. They asynchronously measure per-pixel brightness changes, and output a stream of digital events (or spikes) that encode the time, location and sign of the brightness changes.

## Principle of Operation {#principle-of-operation}

Standard cameras acquire full images at a rate specified by an external clock (e.g. 30fps). Event-cameras respond to brightness changes in the scene asynchronously and independently for every pixel. The output is a variable data-rate sequence of spikes, each event representing a change of brightness (log intensity).

An event consists of the location (x, y), the time (t), and the polarity of the change (p).

The incident light at a pixel is a product of scene illumination and surface reflectance. Hence, changes in log intensity in the scene generally signals a reflectance change (e.g. as a result of movement in the scene), as the illumination is usually constant. This is why event cameras are invariant to scene illumination.

## [DVS Cameras]({{< relref "dvs_cameras" >}}) {#dvs-cameras--dvs-cameras-dot-md}

DVS Cameras followed a frame-based silicon retina design, where the continuous-time photoreceptor was coupled to a readout circuit that was reset each time the pixel was sampled ([Gallego et al. 2020](#orgd5a886a)).

DVS events (x, y, t, p) can be used in many applications, but some also require static output (i.e. absolute brightness). Cameras have been developed to concurrently output both dynamic and static information.

## Benefits {#benefits}

High Temporal Resolution
: Monitoring brightness changes is fast in analog circuitry. The read-out of events is digital with a 1MHz clock, so events are detected and timestamped at the microsecond resolution. Hence, they can capture fast motions without motion blur.

Low Latency
: Each pixel operates independently, and can transmit log intensity changes upon detection.

Low Power Consumption
: Event cameras transmit only changes in brightness and removes redundant data.

High Dynamic Range
: Event cameras can acquire information in different lighting conditions, due to the fact that photoreceptors operate in logarithmic scale, and that each pixel operates independently, not waiting for a global shutter

## Event Generation Model {#event-generation-model}

INdependent pixels respond to changes in their log photocurrent \\(L = \log (I)\\). In a noise-free scenario, an event \\((\boldsymbol{x}\_{k}, t\_{k}, p\_{k})\\) is generated as soon as the brightness increment since the last event at the pixel:

\begin{equation}
\Delta L(\boldsymbol{x}\_{k}, t\_{k}) = L(\boldsymbol{x\_{k}}, t\_{k}) - L(\boldsymbol{x}\_{k}, t\_{k} - \Delta t\_{k})
\end{equation}

reaches a temporal contrast threshold \\(\pm C\\):

\begin{equation}
\Delta L(\boldsymbol{x}\_{k}, t\_{k}) = p\_{k} C
\end{equation}

where \\(C > 0\\), and \\(\Delta t\_{k}\\) is the time elapsed since the last event at the same pixel, and \\(p\_{k} \in \\{+1, -1\\}\\) is the polarity of the brightness change.

The contrast sensitivity \\(C\\) is determined by pixel bias currents, which set the speed and threshold voltages of the change detector, and are generated by an on-chip digitally programmed bias generator. Typical [DVS Cameras]({{< relref "dvs_cameras" >}}) can set the threshold to 10%-50% illumination change.

## [Event Representations]({{< relref "event_representations" >}}) {#event-representations--event-representations-dot-md}

Events are processed and transformed into alternative representations to facilitate the extraction of meaningful information to solve a given task.

**Individual Events** \\(e\_{k} = (x\_{k}, t\_{k}, p\_{k})\\) are used by [probabilistic filters]({{< relref "probabilistic_filters" >}}) and [Spiking Neural Networks]({{< relref "spiking_neural_networks" >}}). Filters and SNNs fuse incoming events asynchronously to produce and output. (7, 24, 61, 96, 97)

**Event Packet** Events \\(\mathcal{E} \doteq\left\\{e\_{k}\right\\}\_{k}^{N\_{e}}\\) in a spatio-temporal neighbourhood are processed together to produce an output. Precise timestamp and polarity information is retained. Choosing the appropriate packet size \\(N\_{e}\\) is crucial to satisfy assumptions of the algorithm.

**Event Frame** The events are converted in a simple way (e.g. pixel-wise by counting events, accumulating polarity) into an image, that can be fed into image-based computer vision algorithms. This discards sparsity, quantizes timestamps, and results in images that are sensitive to the number of events received. Hence, this is not the ideal representation, but have an intuitive interpretation and are compatible with traditional computer-vision algorithms.

**[Time surface (TS)]({{< relref "time_surface_ts" >}})** A TS is a 2D map where each pixel stores a single time value. Events are converted into an image whose intensity is a function of the motion history at that location, with brighter values corresponding to more recent motion. TSs highly compress information as they only keep one timestamp per pixel, and their effectiveness degrades on textured scenes, where pixels spike frequently.

**[Voxel Grid]({{< relref "voxel_grid" >}})** is a space-time (3D) histogram of events, where each voxel represents a particular pixel and time interval. This representation preserves the temporal information of events by avoiding to collapse them on a 2D grid.

**3D Point Set** Events in a spatio-temporal neighbourhood are treated as points in 3D space \\((x\_{k}, y\_{k}, t\_{k})\\). The temporal dimension is transformed into a geometric one, to be used with point-based geometric processing methods such as PointNet.

**Point sets on image plane** Events are treated as an evolving set of 2D points on the image plane. Early shape tracking methods such as mean-shift or ICP work on this data.

**Motion-compensated event image** This representation depends not only on events but also on motion hypothesis.

**Reconstructed Images** Brightness images can be obtained by image reconstruction, that can be interpreted as a motion-invariant representation.

### Methods For Event Processing {#methods-for-event-processing}

<!--list-separator-->

- Event-by-event-based methods

  Deterministic filters such as space-time convolutions and activity filters have been used for noise reduction, feature extraction, image reconstruction, and brightness filtering. Probabilistic filters such as [Kalman Filter]({{< relref "kalman_filter" >}}) and [Particle Filter]({{< relref "particle_filter" >}}) have been used for pose tracking in [SLAM]({{< relref "slam" >}}). Incoming events are compared against additional information to update the filter state.

  Alternatively, multi-layer ANNs that take in frames are trained using gradient-based methods, and then converted into SNNs that process data event-by-event.

<!--list-separator-->

- Methods for Groups of Events

## \_ {#}

## Bibliography {#bibliography}

<a id="orgd5a886a"></a>Gallego, Guillermo, Tobi Delbruck, Garrick Orchard, Chiara Bartolozzi, Brian Taba, Andrea Censi, Stefan Leutenegger, et al. 2020. “Event-Based Vision: A Survey.” _arXiv:1904.08405 [Cs]_, February.