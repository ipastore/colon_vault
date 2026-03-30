### Mensaje de Morlana

#### esto es para guardar los cudasift, tendras q hacer algo similar supongo
```cpp
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <cudaImage.h>
#include <cudaSift.h>
#include <iostream>
#include <fstream>
#include <string>
#include <filesystem>

using namespace std;
namespace fs = std::filesystem;

// Function to save SIFT features to a text file
void saveSiftFeatures(const SiftData& siftData, const string& filename) {
    ofstream file(filename);
    if (!file.is_open()) {
        cerr << "Error: Could not open file " << filename << " for writing." << endl;
        return;
    }
    
    // Write header with number of features
    file << "# SIFT Features" << endl;
    file << "# Format: x y scale orientation score data[128]" << endl;
    file << "# Number of features: " << siftData.numPts << endl;
    
    // Write each SIFT feature
    for (int i = 0; i < siftData.numPts; i++) {
        const SiftPoint& pt = siftData.h_data[i];
        
        // Write position, scale, orientation, and score
        file << pt.xpos << " " << pt.ypos << " " << pt.scale << " " 
             << pt.orientation << " " << pt.score;
        
        // Write 128-dimensional descriptor
        for (int j = 0; j < 128; j++) {
            file << " " << pt.data[j];
        }
        file << endl;
    }
    
    file.close();
    cout << "Saved " << siftData.numPts << " SIFT features to " << filename << endl;
}

// Function to save ONLY the 128-D SIFT descriptors to CSV (one row per feature)
void saveSiftDescriptorsCSV(const SiftData& siftData, const string& filename) {
    ofstream file(filename);
    if (!file.is_open()) {
        cerr << "Error: Could not open file " << filename << " for writing." << endl;
        return;
    }

    // Header
    file << "# SIFT Descriptors (one row per feature, 128 comma-separated floats)" << endl;
    file << "# num_features=" << siftData.numPts << ", descriptor_dim=128" << endl;

    for (int i = 0; i < siftData.numPts; i++) {
        const SiftPoint& pt = siftData.h_data[i];
        for (int j = 0; j < 128; j++) {
            file << pt.data[j];
            if (j < 127) file << ",";
        }
        file << endl;
    }

    file.close();
    cout << "Saved descriptors CSV to " << filename << endl;
}

// Function to save SIFT features in binary format
void saveSiftFeaturesBinary(const SiftData& siftData, const string& filename) {
    ofstream file(filename, ios::binary);
    if (!file.is_open()) {
        cerr << "Error: Could not open file " << filename << " for writing." << endl;
        return;
    }
    
    // Write number of features
    int numFeatures = siftData.numPts;
    file.write(reinterpret_cast<const char*>(&numFeatures), sizeof(int));
    
    // Write each SIFT feature
    for (int i = 0; i < siftData.numPts; i++) {
        const SiftPoint& pt = siftData.h_data[i];
        file.write(reinterpret_cast<const char*>(&pt), sizeof(SiftPoint));
    }
    
    file.close();
    cout << "Saved " << siftData.numPts << " SIFT features to binary file " << filename << endl;
}

// Function to save a compact binary with (x, y, scale, orientation, 128-d descriptor) per point
// Layout: uint32 N, then for i in [0..N): float32 x, float32 y, float32 scale, float32 orientation, float32 desc[128]
void saveSiftXYDescriptorBinary(const SiftData& siftData, const string& filename) {
    ofstream file(filename, ios::binary);
    if (!file.is_open()) {
        cerr << "Error: Could not open file " << filename << " for writing." << endl;
        return;
    }

    // Write number of features
    uint32_t numFeatures = static_cast<uint32_t>(siftData.numPts);
    file.write(reinterpret_cast<const char*>(&numFeatures), sizeof(uint32_t));

    // Write per-feature compact record: x, y, scale, orientation, descriptor[128]
    for (int i = 0; i < siftData.numPts; i++) {
        const SiftPoint& pt = siftData.h_data[i];
        const float x = pt.xpos;
        const float y = pt.ypos;
        const float s = pt.scale;
        const float o = pt.orientation;
        file.write(reinterpret_cast<const char*>(&x), sizeof(float));
        file.write(reinterpret_cast<const char*>(&y), sizeof(float));
        file.write(reinterpret_cast<const char*>(&s), sizeof(float));
        file.write(reinterpret_cast<const char*>(&o), sizeof(float));
        file.write(reinterpret_cast<const char*>(pt.data), sizeof(float) * 128);
    }

    file.close();
    cout << "Saved compact binary (x,y,scale,orientation,desc) to " << filename << endl;
}

static bool process_single_image(const string &image_path, const string &output_format) {
    SiftData siftData;
    InitSiftData(siftData, 25000, true, true);

    cv::Mat img_color = cv::imread(image_path, cv::IMREAD_COLOR);
    if (img_color.empty()) {
        cerr << "Error: Could not load image " << image_path << endl;
        FreeSiftData(siftData);
        return false;
    }

    cv::Mat img_gray;
    cv::cvtColor(img_color, img_gray, cv::COLOR_BGR2GRAY);
    cv::Mat limg;
    img_gray.convertTo(limg, CV_32FC1);

    CudaImage cuda_img;
    cuda_img.Allocate(limg.cols, limg.rows, limg.cols, false, NULL, (float*)limg.data);
    cuda_img.Download();

    int numOctaves = 3;
    float initBlur = 2.0f;
    float thresh = 0.4f;
    float minScale = 0.0f;
    bool upScale = false;

    ExtractSift(siftData, cuda_img, numOctaves, initBlur, thresh, minScale, upScale);

    string base_name = image_path.substr(0, image_path.find_last_of('.'));
    string output_file;

    if (output_format == "binary") {
        output_file = base_name + "_sift_full.bin";
        saveSiftFeaturesBinary(siftData, output_file);
    } else {
        output_file = base_name + "_sift.txt";
        saveSiftFeatures(siftData, output_file);
    }

    string xydesc_bin = base_name + "_sift.bin";
    saveSiftXYDescriptorBinary(siftData, xydesc_bin);

    FreeSiftData(siftData);
    return true;
}

int main(int argc, char* argv[]) {
    if (argc < 2) {
        cerr << "Usage: " << argv[0] << " <root_folder_or_image> [output_format]" << endl;
        cerr << "  If a folder is provided, all *.png files are processed recursively." << endl;
        cerr << "  output_format: 'text' or 'binary' (default: text)" << endl;
        return -1;
    }

    string input_path = argv[1];
    string output_format = (argc > 2) ? argv[2] : "text";

    // Initialize CUDA once
    InitCuda(0);

    size_t processed = 0;
    size_t failed = 0;

    if (fs::is_directory(input_path)) {
        for (auto const &entry : fs::recursive_directory_iterator(input_path)) {
            if (!entry.is_regular_file()) continue;
            const fs::path p = entry.path();
            if (p.has_extension()) {
                string ext = p.extension().string();
                // Treat .png and .PNG as valid
                if (ext == ".png" || ext == ".PNG") {
                    const string imgPath = p.string();
                    cout << "Processing: " << imgPath << endl;
                    bool ok = process_single_image(imgPath, output_format);
                    if (ok) processed++; else failed++;
                }
            }
        }
    } else {
        // Single image path
        bool ok = process_single_image(input_path, output_format);
        processed += ok ? 1 : 0;
        failed += ok ? 0 : 1;
    }

    cout << "Done. Processed=" << processed << ", Failed=" << failed << endl;
    return (failed == 0) ? 0 : 1;
}
```

#### est en system linea 688 creo que puedes extraer esa informacion de images.txt tb

```bash
void System::SaveInColmapTextFormatPointsMap(std::string &folder, std::vector<MapPoint*> &vpMPs, bool isDense)  
{  
    int meanObs = 0;  
    int numValidMPs = 0;  
    for (int i = 0; i < vpMPs.size(); ++i)  
    {  
        MapPoint* pMPi = vpMPs[i];  
        if(!pMPi || pMPi->isBad())  
        {  
            continue;  
        }        meanObs += pMPi->GetObservations().size();  
        numValidMPs++;  
    }  
    if(numValidMPs == 0)  
    {  
        Logger::Logd("System","SaveInColmapTextFormatPointsMap",logger::Logger::RED)<<"No valid points found.";  
        return;  
    }  
    meanObs = meanObs / numValidMPs;    string filename = isDense ? folder + "densePoints3D.txt" : folder + "points3D.txt";  
    ofstream f;  
    f.open(filename.c_str());  
    f << fixed;  
    f << "# 3D point list with one line of data per point:\n";  
    f << "#   POINT3D_ID, X, Y, Z, R, G, B, ERROR, TRACK[] as (IMAGE_ID, POINT2D_IDX)\n";  
    f << "# Number of points: " << numValidMPs << ", mean track length: " << meanObs << "\n";  
    for (int i = 0; i < vpMPs.size(); ++i)  
    {  
        MapPoint* pMPi = vpMPs[i];  
        if(!pMPi || pMPi->isBad())  
        {  
            continue;  
        }        string strMPID = to_string(pMPi->mnId);  
        string strPose = pMPi->GetWorldPosColmapFormat();  
        string strColor = "0 0 0"; // We don't store the color yet  
        string strError = "0";  
        string strObservations = "";        f << strMPID << " " << strPose << " " << strColor << " " << strError << " " << strObservations << "\n";  
    }    f.close();  
}
```
