# fruit-classification-starter

<img width="300" alt="スクリーンショット 2022-12-30 11 17 45" src="https://user-images.githubusercontent.com/47273077/210028043-b4ea69ae-e3c6-4aa1-9ec0-f5e1ea790d9c.gif">


## Download Fruit Dataset
https://www.kaggle.com/datasets/moltean/fruits?resource=download

## Set up Model
### ■How to Create ML
<img width="1367" alt="Open_Developer_Tool_と__と_Item-0" src="https://user-images.githubusercontent.com/47273077/209740199-21362d99-4a37-4696-a502-fb6e8c1bc32e.png">

<img width="771" alt="スクリーンショット_2022_12_28_9_43" src="https://user-images.githubusercontent.com/47273077/209740418-38dab1a1-7bb7-43f0-a168-6eda9ef2e919.png">

<img width="1052" alt="スクリーンショット_2022_12_28_9_43" src="https://user-images.githubusercontent.com/47273077/209740506-23867f73-db73-4ff4-818e-3ff491a898ab.png">

<img width="1050" alt="スクリーンショット_2022_12_28_9_45" src="https://user-images.githubusercontent.com/47273077/209740622-49e97345-2a9e-4165-a87f-bda224b072ae.png">

### ■Set up Images
<img width="1418" alt="スクリーンショット_2022_12_28_9_47" src="https://user-images.githubusercontent.com/47273077/209740805-c04391b8-c4c8-44c2-becb-cdd44854406b.png">

### ■Train Model

<img width="1406" alt="スクリーンショット_2022_12_28_9_52" src="https://user-images.githubusercontent.com/47273077/209741056-4b80fac1-4c6c-4b60-936a-b0cb495fda4e.png">

## Test Model
<img width="1316" alt="スクリーンショット_2022_12_28_10_16" src="https://user-images.githubusercontent.com/47273077/209742533-b32896a5-6eca-4b7a-967d-88bdebc57b47.png">

<img width="1422" alt="スクリーンショット_2022_12_28_10_17" src="https://user-images.githubusercontent.com/47273077/209742652-f24157f4-d507-47c2-a542-58e136e692bf.png">

## Export Model
<img width="1361" alt="スクリーンショット_2022_12_28_11_00" src="https://user-images.githubusercontent.com/47273077/209745626-4e92311f-e5a3-4099-a3ac-29a20ffcd073.png">

## Implement Source code

ViewController
```swift
    private let classifier = VisionClassfier(mlModel: FruitClassifier_1().model)

      Button("Classify") {

      if let img = self.image {
          self.classifier?.classify(img) { result in
              self.classificationLabel = result
          }
      }

  }.padding()
      .foregroundColor(Color.white)
      .background(Color.green)
      .cornerRadius(10)
```
                    

VisionClassfier
```swift
import Foundation
import CoreML
import Vision
import UIKit

class VisionClassfier {
    
    private let model: VNCoreMLModel
    private var completion: (String) -> Void = { _ in }
    
    private lazy var request: [VNCoreMLRequest] = {
        
        let request = VNCoreMLRequest(model: model) { (request, error) in
            guard let results = request.results as? [VNClassificationObservation] else {
                return
            }
            
            if !results.isEmpty {
                if let result = results.first {
                    self.completion(result.identifier)
                }
            }
        }
        
        request.imageCropAndScaleOption = .centerCrop
        return [request]
    }()
    
    init?(mlModel: MLModel) {
        if let model = try? VNCoreMLModel(for: mlModel) {
            self.model = model
        } else {
            return nil
        }
    }
    
    func classify(_ image: UIImage, completion: @escaping (String) -> Void) {
        
        self.completion = completion
        
        DispatchQueue.global().async {
            
            guard let cgImage = image.cgImage else {
                return
            }
            
            let handler = VNImageRequestHandler(cgImage: cgImage, options: [:])
            
            do {
                try handler.perform(self.request)
            } catch {
                print(error.localizedDescription)
            }
        }
    }
}

```
