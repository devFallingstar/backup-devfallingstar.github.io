---
layout: post
title: "[Swift 3.0, Firebase] UIImagePickerViewController를 이용한 이미지 업로드시 에러"
date: "2017-04-19 02:07:15 +0900"
---

파이어베이스 Storage에 이미지를 업로드 하려던 중, 다음과 같은 오류를 만났다.
{% highlight language %}
Body file is unreachable: /asset.JPG  
Error Domain=NSCocoaErrorDomain Code=4 "The file doesn’t exist."  
{% endhighlight %}}

해석하자면, asset.JPG(내가 선택한 이미지 파일)이 존재하지 않기 때문에, 파일을 불러올 수 없다.
라는 내용인데...

코드 부분을 살펴보도록하자.

{% highlight language %}
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
    let image = info[UIImagePickerControllerOriginalImage] as! UIImage
    let imageUrl = info[UIImagePickerControllerReferenceURL] as? URL
    let imageName = imageUrl?.lastPathComponent //1
    dismiss(animated: true, completion: nil)
    let filePath = storageRef.child("userFile").child((FIRAuth.auth()?.currentUser!.uid)!).child(((imageName)!)) //2
    let metaData = FIRStorageMetadata()
    metaData.contentType = "image/jpg"

    filePath.putFile(imageUrl!, metadata: metaData) { (mData, error) in //3
      if let err = error {
        DDHelper.Log(withLogType: 0, LogText: err.localizedDescription, VarName: "")
        print("NOT ELSE")
        return
      }else{
        let downloadURL = mData!.downloadURL()!.absoluteString
        self.sendMessage(withText: "[mImageFile]\(downloadURL)")
        print("ELSE")
      }
    }
}
{% endhighlight %}

image 상수에는 UIImagePickerController가 종료될 때 얻어온 OriginalImage의 정보를 얻고, imageUrl 상수에는 Picker에서 골랐던 이미지의 URL을 넣었다. 그런데 이미지를 이미지라 부르지 못한다고? 이게 무슨 홍길동 같은 상황인가...

하지만 이것 또한 결국 내 무지에서 비롯된 결과였다.

스위프트의 UIImagePickerController에서는 총 두 가지 방법으로 사진 정보를 얻어올 수 있다. info[UIImagePickerControllerOriginalImage]와 info[UIImagePickerControllerReferenceURL]이 그 방법이다. 이 방식은 크게 '앨범'에서 가져올 때와 '카메라'에서 가져올 때를 기준으로 나뉘어질 수 있는데, 앨범에서 가져올 때는 후자의 방식을 이용해서 가져와야한다는 것이다.

참고로 제작 중인 어플리케이션은 앨범과 카메라 두 개의 소스를 모두 사용하지만, 당시 코드를 짤 때는 앨범으로만 테스트를 진행하였기에 UIImagePickerControllerReferenceURL을 사용했어야 맞았던 것이다.

따라서, 카메라와 앨범 등 다양한 상황에 맞게 이미지를 업로드 할 수 있도록 다음과 같이 코드를 변경하였다.
{% highlight language %}
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
        // if it's a photo from the library, not an image from the camera
        if #available(iOS 8.0, \*), let referenceUrl = info[UIImagePickerControllerReferenceURL] as? URL {
            let assets = PHAsset.fetchAssets(withALAssetURLs: [referenceUrl], options: nil)
            let asset = assets.firstObject
            asset?.requestContentEditingInput(with: nil, completionHandler: { (contentEditingInput, info) in
                let imageFile = contentEditingInput?.fullSizeImageURL

                do {
                    if let image = UIImage(data: try Data(contentsOf: imageFile!)) {
                        let imageComp = self.resizeImage(image: image, targetSize: CGSize(width: image.size.width/9, height: image.size.height/9))
                        guard let imageData = UIImagePNGRepresentation(imageComp) else {
                            return
                        }
                        DDHelper.Log(withLogType: 2, LogText: "x : \(imageComp.size.width), y : \(imageComp.size.height)", VarName: "SIZE Lib")
                        DDHelper.Log(withLogType: 2, LogText: imageData.base64EncodedString(), VarName: "File Size Lib")

                        let imagePath = "/\(Int(Date.timeIntervalSinceReferenceDate * 1000)).jpg"
                        // [START uploadimage]
                        let metadata = FIRStorageMetadata()
                        metadata.contentType = "image/jpeg"
                        self.storageRef.child("userFile").child((FIRAuth.auth()?.currentUser!.uid)!).child(imagePath)
                            .put(imageData, metadata: metadata) { (metadata, error) in
                                if let error = error {
                                    print("Error uploading: \(error)")
                                    return
                                }

                                if let downURL = metadata?.downloadURL()?.absoluteString{
                                    self.sendMessage(withText: "[mImageFile]\(downURL)")
                                }
                        }
                    }
                }catch{
                    DDHelper.Log(withLogType: 2, LogText: error.localizedDescription, VarName: "IMG Load")
                }
                // [END uploadimage]
            })
        } else {
            guard let image = info[UIImagePickerControllerOriginalImage] as? UIImage else { return }
            let imageComp = self.resizeImage(image: image, targetSize: CGSize(width: image.size.width/9, height: image.size.height/9))
            guard let imageData = UIImagePNGRepresentation(imageComp) else {
                return
            }

            DDHelper.Log(withLogType: 2, LogText: "x : \(imageComp.size.width), y : \(imageComp.size.height)", VarName: "SIZE")
            DDHelper.Log(withLogType: 2, LogText: imageData.base64EncodedString(), VarName: "File Size")

            let imagePath = "/\(Int(Date.timeIntervalSinceReferenceDate * 1000)).jpg"
            let metadata = FIRStorageMetadata()
            metadata.contentType = "image/jpeg"
            self.storageRef.child("userFile").child((FIRAuth.auth()?.currentUser!.uid)!).child(imagePath)
                .put(imageData, metadata: metadata) { (metadata, error) in
                    if let error = error {
                        print("Error uploading: \(error)")
                        return
                    }

                    if let downURL = metadata?.downloadURL()?.absoluteString{
                        self.sendMessage(withText: "[mImageFile]\(downURL)")
                    }
            }
        }
        picker.dismiss(animated: true, completion: nil)
}
{% endhighlight %}

해당 포스트는 [파이어베이스 공식 github](https://github.com/firebase/quickstart-ios/blob/master/messaging/MessagingExampleSwift/AppDelegate.swift#L69-L98)를 참조하여 작성했습니다.
