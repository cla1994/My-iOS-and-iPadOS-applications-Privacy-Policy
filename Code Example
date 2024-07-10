//
//  AddNewCoin.swift
//  NewCoinCollection
//
//  Created by Claudio Menichini on 19/06/22.
//

import UIKit
import AVKit
import Photos

// controller per aggiungere monete

class AddNewCoin: UIViewController, UIImagePickerControllerDelegate & UINavigationControllerDelegate {     

    @IBOutlet weak var frontImage: UIImageView!
    @IBOutlet weak var backImage: UIImageView!
    @IBOutlet weak var currencyIssuer: UITextField!
    @IBOutlet weak var material: UITextField!
    @IBOutlet weak var year: UITextField!
    @IBOutlet weak var details: UITextView!
    @IBOutlet weak var name: UITextField!
    
    let coin = Coin()

    // booleano per sapere quale immagine tra fronte e retro sto salvando

    var addingFrontImage = false    
                                     
    
    override func viewDidLoad() {
        super.viewDidLoad()

         // metto bordo text view come quello delle text field

        let borderColor : UIColor = UIColor(red: 0.85, green: 0.85, blue: 0.85, alpha: 1.0)    
        details.layer.borderWidth = 0.5
        details.layer.borderColor = borderColor.cgColor
        details.layer.cornerRadius = 5.0

    }

     // button per salvare

    @IBAction func addNewCoin(_ sender: UIBarButtonItem) {   
                                                                      
       // controlli che verificano che i campi siano stati inseriti

        guard name.text != "" else {           
            name.placeholder = "insert valid data"
            return
        }
        guard currencyIssuer.text != "" else {
            currencyIssuer.placeholder = "insert valid data"
            return
        }
        guard material.text != "" else {
            material.placeholder = "insert valid data"
            return
        }
        guard year.text != "" else {
            year.placeholder = "insert valid data"
            return
        }

        // prendo i dati inseriti dall'utente e li salvo dentro la classe coin accedendo al Singleton           

        coin.currencyIssuer = currencyIssuer.text    
        coin.material = material.text
        coin.details = details.text
        coin.name = name.text
        coin.year = year.text
        CoinsPersistenceStore.shared.saveCoin(coin: coin)
        let alert = UIAlertController(title: "", message: "successfully added coin", preferredStyle: .alert)
        alert.addAction(.init(title: "Ok",
                                      style: .cancel,
                                      handler: {(paramAction:UIAlertAction!) in
            self.navigationController?.popViewController(animated: true)
        }))
        present(alert, animated: true, completion: nil)
    }

    // funzione per prendere l'immagine dalla camera scattando una foto o dalla galleria

    func addImage(){            
        if PHPhotoLibrary.authorizationStatus() != .authorized {
                    PHPhotoLibrary.requestAuthorization { status in
                        guard status == .authorized else {
                            return
                        }
                    }
        }



        // menu con le possibili scelte per selezionare la foto
                
        let attachmentsMenu = UIAlertController(title: nil, message: nil, preferredStyle: .actionSheet)

        // prima scelta, galleria     
                                                                   
        let mediaAction = UIAlertAction(title: "Choose photo",                  
                                        style: .default,
                                        handler: { (action:UIAlertAction!) -> Void in
            let imagePicker = UIImagePickerController()
                    
            imagePicker.delegate = self
            imagePicker.sourceType = .photoLibrary
            imagePicker.allowsEditing = true
            imagePicker.mediaTypes = ["public.image"]
            if UIImagePickerController.isSourceTypeAvailable(.photoLibrary) {
                self.present(imagePicker, animated: true, completion: nil)
            } else {
                let alertPhotoLibraryNotAvailable = UIAlertController(title: "",
                                                                      message: NSLocalizedString("conversation.photoVideoLibraryNotAvailable",
                                                                      comment: "Photo & Video Library not available"),
                                                                      preferredStyle: .alert)
                alertPhotoLibraryNotAvailable.addAction(UIAlertAction(title: "ok", style: .default, handler: nil))
                                
                self.present(alertPhotoLibraryNotAvailable, animated: true, completion: nil)
            }
        })

        // seconda scelta, fotocamera

        let cameraAction = UIAlertAction(title: "Take a picture",           
                                         style: .default,
                                         handler: { (action:UIAlertAction) -> Void in
            let cameraPicker = UIImagePickerController()
            cameraPicker.delegate = self
            cameraPicker.sourceType = .camera
            cameraPicker.allowsEditing = true
            cameraPicker.mediaTypes = ["public.image"]
                        
            if UIImagePickerController.isSourceTypeAvailable(.camera) {
                self.present(cameraPicker, animated: true, completion: nil)
            } else {
                let alertCameraNotAvailable = UIAlertController(title: "", message: NSLocalizedString("conversation.cameraNotAvailable", comment: "camera is not available"), preferredStyle: .alert)
                alertCameraNotAvailable.addAction(UIAlertAction(title: NSLocalizedString("general.ok",
                                                                                          value:"OK",
                                                                                          comment:"OK"),
                                                                style: .default,
                                                                handler: nil))
                self.present(alertCameraNotAvailable, animated: true, completion: nil)
            }
        }
)

        // terza scelta, cancella (torna indietro)

        let cancelAction = UIAlertAction(title: "Cancel", style: .destructive)      
        attachmentsMenu.addAction(cameraAction)
        attachmentsMenu.addAction(mediaAction)
        attachmentsMenu.addAction(cancelAction)
        self.present(attachmentsMenu, animated: true)

    }

    // funzione che crea variabile di tipo URL e salva l'immagine scelta come URL

    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
        guard let image = info[.editedImage] as? UIImage else {
            return
        }                                      
 
        
        var imageData = Data()
        var imageExtension = ""
        var imgURL: URL!
        
     
        if picker.sourceType == UIImagePickerController.SourceType.photoLibrary {
            guard let imageUrl = info[.imageURL] as! URL? else {
                return
            }
            if imageUrl.pathExtension == "PNG" || imageUrl.pathExtension == "png" {
                imageData = image.pngData()!
            } else {
                imageData = image.jpegData(compressionQuality: 0.8)!
                imageExtension = "jpeg"
            }
        } else {
            imageData = image.jpegData(compressionQuality: 0.8)!
            imageExtension = "jpeg"
        }
        
        let imageID = Int(Date().timeIntervalSinceReferenceDate)
        let fileName = "NewCoinCollection-\(imageID).\(imageExtension)"
                
        let dir = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
        imgURL = dir.appendingPathComponent(fileName)
        if addingFrontImage {
            frontImage.image = image
            addingFrontImage = false
            coin.frontImage = imgURL
        } else {
            backImage.image = image
            coin.backImage = imgURL
        }
        
        do {
            try imageData.write(to: imgURL)
            print("[\(#function)] line:\(#line) File saved successfully in: \(imgURL!)")
            picker.dismiss(animated: true, completion: nil)
        } catch {
            picker.dismiss(animated: true, completion: nil)
        }
    }

    // i due button per richiamare la funzione sopra per il fronte e per il retro

    @IBAction func addFrontImage(_ sender: UIButton) {     
        addingFrontImage = true
        addImage()
    }
    @IBAction func addBackImage(_ sender: UIButton) {
        addingFrontImage = false
        addImage()
    }
}
