# Content-Aware Video Cropping

## Challenge Overview

The purpose of this project is to design a Python-based solution for content-aware cropping of landscape videos. This process transforms wide-format videos into portrait mode (with a 9:16 aspect ratio) while preserving and emphasizing the most relevant portions of the video. This functionality is essential for adapting videos to vertical platforms like social media stories or mobile-first content consumption.

## Detailed Breakdown of Components

### Libraries and Dependencies

The project leverages multiple Python libraries to achieve the desired results:

- **Ultralytics YOLO**: This library is used for real-time object detection. It identifies key elements in each video frame, such as people, animals, or objects of interest.
- **Supervision**: This library aids in video handling and annotation.
- **MoviePy**: Used for video processing, including tasks like extracting and merging audio tracks, handling video clips, and ensuring synchronization between the video and its audio.
- **OpenCV**: Facilitates frame-by-frame video analysis and manipulation. It is crucial for resizing, cropping, and applying transformations.
- **Torch**: As a deep learning library, Torch is essential for leveraging GPU-accelerated computations, particularly for object detection tasks.
- **JSON**: Handles structured data storage, ensuring that detection results for each frame can be stored and processed efficiently.

### Workflow Overview

1. **Extract Video Metadata**: The program begins by extracting metadata such as video dimensions (width and height), frames per second (FPS), and the total number of frames. These properties guide subsequent operations like frame resizing and cropping.
2. **Object Detection**: The YOLO model processes video frames in batches to detect objects. This ensures efficient handling of large video files while minimizing computational overhead. Detected objects are categorized and stored along with their bounding box coordinates.
3. **Transform Detection Data**: The detection results are transformed into a format suitable for cropping. This transformation isolates bounding boxes and identifies regions of interest within the video.
4. **Calculate Cropping Dimensions**: Using a target aspect ratio of 9:16, the cropping dimensions are calculated. If no objects are detected in a frame, the crop defaults to the center of the frame. For frames with objects, the crop focuses on the largest object or the most prominent region.
5. **Smooth Crop Transitions**: To ensure a visually smooth experience, the coordinates of the cropping window are smoothed across consecutive frames. This prevents abrupt movements that can be distracting to viewers.
6. **Video Output**: The cropped frames are resized to 9:16 and compiled into a new video. The original audio is synchronized with the edited video to produce a seamless final output.
7. **File Management**: The output file is saved in the desired location, ensuring easy access and usability.

### Key Features

- **Adaptive Cropping**: The cropping logic adapts dynamically based on the content of each frame. This ensures that the most relevant subjects remain in focus, regardless of their position in the original landscape video.
- **Efficient Batch Processing**: Frames are processed in batches to optimize memory and computational resource usage. This approach is particularly beneficial for high-resolution videos or videos with many frames.
- **Smoothing Mechanism**: A smoothing algorithm ensures that the crop transitions between frames are gradual. This prevents jarring shifts and enhances the overall viewing experience.
- **Audio Integration**: The solution retains the original audio track of the video. If the durations of the cropped video and the original audio differ, the audio is trimmed or looped to match the video’s length.

## Justification for Using YOLOv11 Segmentation

When deciding on the algorithm to use for content-aware video cropping, multiple options were evaluated to balance accuracy, computational efficiency, and adaptability to the task. Below is an in-depth explanation of the reasoning and experimentation behind selecting YOLOv11 Segmentation as the final model.

### Experimentation with YOLOv8

Initially, YOLOv8 was chosen for its reputation in object detection tasks. However, during testing, it became apparent that YOLOv8 struggled to detect all objects within the video frames reliably. This limitation was critical, as the primary objective of the project was to ensure precise identification and tracking of all key objects across frames for effective cropping. Due to these shortcomings, alternative solutions were explored.

### Exploration of SAM (Segment Anything Model)

The Segment Anything Model (SAM) was then evaluated for its segmentation capabilities. SAM provides a robust approach to image segmentation, requiring minimal user input for object identification. To initialize SAM, object coordinates were generated using the Claude API. The process involved marking pixels on an image and sending them to the Claude API, which returned the necessary initialization coordinates for SAM. However, this approach had two significant drawbacks:

- **Occasional Mistakes by the LLM**: The Claude API was prone to errors, leading to inconsistent initialization of object points.
- **High Computational Requirements**: The process was computationally expensive, making it impractical for real-time or resource-constrained environments.

Given these challenges, SAM was deemed unsuitable for this application, prompting a shift to other models in the YOLO series.

### Transition to YOLOv11 Object Detection

YOLOv11 Object Detection was the next model explored. It introduced several advancements over its predecessors, including improved accuracy and efficiency. However, while the model performed well in detecting objects, a persistent issue was the ever-increasing frame ID, which complicated consistent object tracking across frames. This limitation highlighted the need for a solution specifically tailored for segmentation tasks.

### Selection of YOLOv11 Segmentation

Finally, YOLOv11 Segmentation was adopted as the ideal model for this application. This decision was based on several factors:

- **Accurate Instance Segmentation**: YOLOv11 Segmentation excelled in identifying and delineating individual objects within the video frames, ensuring precise tracking of key elements.
- **Consistency Across Frames**: Unlike the object detection variant, YOLOv11 Segmentation provided more consistent results, crucial for maintaining focus on the most interesting regions during cropping.
- **Computational Efficiency**: By opting for the smallest available version of YOLOv11 Segmentation, computational requirements and processing times were significantly reduced, aligning with the project's resource constraints.

## Additional Algorithm Details

### Overview of YOLOv11 Segmentation

YOLOv11 is the latest iteration in the YOLO (You Only Look Once) series, designed to improve performance across various computer vision tasks, including object detection and instance segmentation. It introduces key innovations such as:

- **C3k2 Block**: Enhances feature extraction with reduced computational overhead.
- **SPPF (Spatial Pyramid Pooling - Fast)**: Improves the model's ability to detect objects at multiple scales.
- **C2PSA (Convolutional Block with Parallel Spatial Attention)**: Boosts accuracy by focusing on essential regions of the image.

These enhancements allow YOLOv11 to achieve superior results while maintaining a lower parameter count compared to its predecessors.

### Effectiveness in Instance Segmentation

Instance segmentation, a task involving the identification and delineation of individual objects, is a strong suit of YOLOv11. This capability was particularly beneficial for the content-aware cropping application, where precise segmentation of key objects is critical.

## Challenges and Solutions

- **Handling Large Video Files**
  - **Challenge**: Processing high-resolution videos or videos with many frames can strain system resources.
  - **Solution**: The batch-processing approach minimizes memory usage by processing a fixed number of frames at a time. Additionally, GPU acceleration is leveraged when available.
- **Centering Crops on Relevant Content**
  - **Challenge**: Identifying the most relevant region in a video frame is complex, especially in crowded or dynamic scenes.
  - **Solution**: YOLO’s object detection capabilities identify and prioritize key objects, ensuring that the cropping algorithm focuses on meaningful content.
- **Audio-Video Synchronization**
  - **Challenge**: Cropped videos often have a different duration than the original, leading to synchronization issues.
  - **Solution**: The audio track is adjusted to match the duration of the cropped video using MoviePy’s subclip features.
- **Abrupt Frame Transitions**
  - **Challenge**: Quick shifts in the crop position can make the video appear unstable.
  - **Solution**: The exponential smoothing mechanism ensures that crop coordinates transition gradually between frames.

## Conclusion

The decision to use YOLOv11 Segmentation was driven by its superior performance in segmentation tasks, consistency across video frames, and computational efficiency. By leveraging the smallest version of the model, the solution achieved a balance between accuracy and resource usage, ensuring effective and efficient content-aware video cropping.

The Content-Aware Video Cropping solution provides a robust framework for transforming landscape videos into portrait mode. By combining advanced object detection, dynamic cropping, and audio synchronization, the system delivers high-quality, visually appealing results. The solution’s adaptability ensures its relevance across various applications, from social media content creation to video analytics.

[Link to drive containing some outputs: Inference](https://drive.google.com/drive/folders/1MI8ma3I_O8dDF5C6Ssx1nCcfKf7MKk6G?usp=sharing)
