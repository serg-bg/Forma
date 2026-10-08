# Forma

Forma is a desktop app for 3D microscopy analysis on Mac and Windows. You open a stack or a folder of stacks, convert it to OME-Zarr, segment it with a deep-learning model, measure the segmented objects, and review or correct the result in napari. No Python setup is needed. The app installs its own analysis tools the first time you ask.

![The Home tab: open one image or a folder workspace, or drop a file on the window](docs/images/home.png)

Forma reads 2D to 5D TIFF, ND2, and CZI files. Segmentation runs on the GPU of an Apple silicon Mac, on an NVIDIA card on Windows, or on the processor. An image larger than memory is segmented in pieces, and a stopped run continues from the last finished piece.

## Download

Get the two files for your computer from the [latest release](https://github.com/serg-bg/Forma/releases/latest).

| Computer | Files | Size |
|---|---|---|
| Apple silicon Mac | `Forma-mac.zip` and `Forma-mac.zip.sha256` | 157 MB |
| Windows PC | `Forma-windows.zip` and `Forma-windows.zip.sha256` | 18 MB, plus 0.5 GB on first start |

The `.sha256` file lets you check that the zip arrived intact. On a Mac, run `shasum -a 256 -c Forma-mac.zip.sha256` in Terminal from the download folder. The Windows download is a preview. See [Known limits](#known-limits).

## Install on a Mac

1. Double-click `Forma-mac.zip`, then move `Forma.app` into **Applications**.
2. Double-click `Forma.app`. macOS blocks it, because Forma is not notarized by Apple.
3. Open **System Settings**, click **Privacy & Security**, scroll down to the Security section, and click **Open Anyway**.
4. Click **Open** in the warning that appears. macOS remembers the choice.

Forma runs on Apple silicon Macs. It was built and tested on macOS 15.

## Install on Windows

1. Download `Forma-windows.zip`. If the browser asks whether to keep the file, keep it.
2. Right-click the zip, choose **Extract All…**, then **Extract**. Extract into a new folder, not over the folder of an earlier version. Forma refuses to start from a folder that mixes two versions.
3. Open the extracted `Forma` folder and double-click `Forma` (type "Windows Command Script"). The download is not signed, so Windows may warn that the file comes from the internet. Choose **Run**, or **More info** and then **Run anyway**.
4. The first start downloads about 0.5 GB and takes a few minutes. A black window shows the progress and closes by itself once the app opens. Later starts open the app directly.

For GPU segmentation, Windows needs an NVIDIA card of the GTX 16 or RTX 20 series or newer, with driver 580 or later. Without one, Forma segments on the processor, which has not been tested.

## Set up the analysis tools and a model

1. In Forma, click **Install analysis tools** at the bottom of the window. This installs napari, the Forma plugins, and the segmentation engine, about 2 GB, once. Keep the window open until it reports that the installation finished.
2. Open the **Segment** tab and pick a model from the **Model** list. The first pick asks for your name, email, and institution, then downloads the model once (1.1 GB) and keeps it on your computer.

A model package already on disk can be chosen with **Choose a local package…** instead. That needs no network.

### The model

**Dataset226 neuronal morphology Global V6** segments one-channel 3D confocal stacks of neurons into dendrite, spine core, spine edge, spine neck, soma, and axon. It expects voxels near 65 nm in XY and 200 nm in Z. It is the model distributed with RESPAN ([Bernal-Garcia et al., Cell Reports Methods 2025](https://doi.org/10.1016/j.crmeth.2025.101179)), where its validation is described. The weights are licensed [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/), for noncommercial use.

## The five tabs

Forma keeps one image or a folder of images in a workspace and takes it through five tabs, in order.

**Home** opens one image, an existing OME-Zarr, or a folder workspace, and resumes a recent one.

**Prepare** lists the images it found and converts the checked ones to multiscale OME-Zarr. It reads the voxel spacing from the file. When the file has none, you type it in.

![The Prepare tab](docs/images/prepare.png)

**Segment** picks the model, the compute device, and the channel, checks the selected images against the model, then runs them one at a time. An image above the model's size limit runs in pieces, with the time left shown as it runs. On the test image the piecewise result was identical to the whole-image result.

**Quantify** measures every object in the segmented images into one table per image and a running summary. The class names come from the model, so nothing in Forma assumes what you imaged.

![The Quantify tab](docs/images/quantify.png)

**Review** opens the workspace, one result, or any OME-Zarr in napari. There, the Forma plugins page through the images of a workspace, edit labels and save them as a new revision beside the original, trace a neuron and export the path as SWC, and, on a Mac, denoise with CARE.

![The Review tab](docs/images/review.png)

## What Forma sends

Forma uses the internet in three places. The analysis-tools installer fetches Python packages from the Python package index and the PyTorch index. Every model request carries the app version, the operating system, and an anonymous id for your installation. Each model download also carries the name, email, and institution you entered once. The authors see these records and use them to know who uses the models, which are licensed for noncommercial use. Nothing else leaves your computer. Your images and results stay where you put them.

## Known limits

- The Windows version is a preview. It has run on one Windows Server machine with an NVIDIA A10G card and on one Windows 11 laptop.
- Windows limits file paths to 260 characters. Forma says so when a data folder is too deep. Keep data folders short and near the top of a drive. A Windows user name longer than about 26 characters stops the engine's installation, and Forma states the reason.
- Results differ slightly between an NVIDIA card and a Mac. The card runs the model in mixed precision, the Mac in full precision. On the test image the two disagree on under 0.1% of the labelled voxels.
- Time series convert and display with their time axis, but segmentation, tracing, and label editing refuse them. Convert a single timepoint for that work.
- Denoising with CARE is part of the Mac app only.
- Forma is not notarized by Apple and not signed on Windows, hence the warnings during installation.

## Feedback

Forma is new and will have rough edges. Report them as [issues](https://github.com/serg-bg/Forma/issues). The issue form asks for the Forma version, your computer, the button you clicked, the input format, and the log text. Do not upload raw microscopy data or model files.

## License

- The Forma app: [GPL-3.0](LICENSE).
- The model weights (Dataset226) and the CARE denoising weights in the Mac app: [CC BY-NC 4.0](LICENSE-WEIGHTS), noncommercial use.
- Third-party components keep their own licenses.

## Cite

If Forma contributes to your work, cite it with the [CITATION.cff](CITATION.cff) file in this repository, and cite the model's paper:

Bernal-Garcia S, Schlotter AP, Pereira DB, Recupero AJ, Polleux F, Hammond LA. A deep learning pipeline for accurate and automated restoration, segmentation, and quantification of dendritic spines. Cell Reports Methods. 2025;5(10):101179. https://doi.org/10.1016/j.crmeth.2025.101179

## Author

Sergio Bernal-Garcia
