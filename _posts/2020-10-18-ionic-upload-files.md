---
title: Ionic 4 - camera nativa e upload de imagem
layout: post
tags: mobile, ionic, upload, file, angular
---
# Ionic 4 - camera nativa e upload de imagem
Criando um projeto em branco

```
ionic start camera_upload blank
```

Após finalizada a criação do projeto, instala-se os plugins necessários

```
ionic cordova plugin add cordova-plugin-camera
npm install @ionic-native/cameraionic
cordova plugin add cordova-plugin-file
npm install @ionic-native/file
```
Em seguida, importamos a Camera e File dentro dos módulo do app e injeta-os na seção do provider. O seu arquivo app module deve estar assim:

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouteReuseStrategy } from '@angular/router';import { IonicModule, IonicRouteStrategy } from '@ionic/angular';
import { SplashScreen } from '@ionic-native/splash-screen/ngx';
import { StatusBar } from '@ionic-native/status-bar/ngx';import { AppComponent } from './app.component';
import { AppRoutingModule } from './app-routing.module';
import { File } from '@ionic-native/file/ngx';
import {HttpClientModule} from '@angular/common/http';
import { Camera } from '@ionic-native/camera/ngx';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [
      BrowserModule,
      IonicModule.forRoot(),
      AppRoutingModule,
      HttpClientModule
  ],
  providers: [
    StatusBar,
    SplashScreen,
    File,
    Camera,
    {
      provide: RouteReuseStrategy,
      useClass: IonicRouteStrategy
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

Agora criamos o serviço do upload
```
ionic g service Upload
```

Importamos o HttpClientModule dentro do app module e injetamos-o dentro na seção de imports.

No serviço criado teremos usamos o código abaixo (lembre-se de alterar o endereço para a url de seu servidor web):

```ts
import { Injectable } from '@angular/core';
import {HttpClient} from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})

export class UploadService {  constructor(private http: HttpClient) { }  uploadFile(formData) {
    return this.http.post('https://example.com/upload.php', formData);
  }
}
```

Agora no classe do componente principal escreve o codigo abaixo:

```ts
import {AfterViewInit, Component, OnInit, ViewChild} from '@angular/core';
import {File, IWriteOptions, FileEntry} from '@ionic-native/file/ngx';
import { Camera, CameraOptions } from '@ionic-native/camera/ngx';
import {UploadService} from '../upload.service';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})

export class HomePage implements OnInit, AfterViewInit {
  options: CameraOptions = {
    quality: 100,
    destinationType: this.camera.DestinationType.FILE_URI,
    encodingType: this.camera.EncodingType.JPEG,
    mediaType: this.camera.MediaType.PICTURE
  };
  constructor(private file: File, private uploadService: UploadService, private camera: Camera) {}  ngOnInit(): void {
  }  ngAfterViewInit(): void {
  }  readFile(file: any) {
    const reader = new FileReader();
    reader.onloadend = () => {
      const imgBlob = new Blob([reader.result], {
        type: file.type
      });
      const formData = new FormData();
      formData.append('name', 'Hello');
      formData.append('file', imgBlob, file.name);
      this.uploadService.uploadFile(formData).subscribe(dataRes => {
        console.log(dataRes);
      });
    };
    reader.readAsArrayBuffer(file);
  }  takePicture() {
    this.camera.getPicture(this.options).then((imageData) => {
      this.file.resolveLocalFilesystemUrl(imageData).then((entry: FileEntry) => {
        entry.file(file => {
          console.log(file);
          this.readFile(file);
        });
      });
    }, (err) => {
      // Handle error
    });
  }
}
```

E no arquivo de template o codigo abaixo:
```
<ion-header [translucent]="true">
  <ion-toolbar>
    <ion-title> Blank </ion-title>
  </ion-toolbar>
</ion-header>

<ion-content [fullscreen]="true">
  <ion-header collapse="condense">
    <ion-toolbar>
      <ion-title size="large">Blank</ion-title>
    </ion-toolbar>
  </ion-header>

  <div id="container">
    <ion-row>
      <ion-col
        ><ion-button color="primary" (click)="takePicture()"
          >Take Picture</ion-button
        ></ion-col
      >
    </ion-row>
  </div>
</ion-content>

```

No servidor php crie dois arquivos:

upload.php
```php
<?php
$name = $_POST['name'];
if ($_FILES['file']) {
    $path = 'uploads/';
    if (!file_exists($path)) {
        mkdir($path, 0777, true);
    }
    $originalName = $_FILES['file']['name'];
    $ext = '.' . pathinfo($originalName, PATHINFO_EXTENSION);
    $t = time();
    $generatedName = md5($t . $originalName) . $ext;
    $filePath = $path . $generatedName;
    if (move_uploaded_file($_FILES['file']['tmp_name'], $filePath)) {
        echo json_encode(array(
            'result' => 'success',
            'status' => true,
        ));
    } else {
        echo json_encode(array(
            'result' => 'error',
            'status' => false,
        ));
    }
}
```

index.php
upload.php
```php
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
  <title>Devdactic Image Upload</title>
</head>

<body>
  <h1>Ionic Image Upload</h1>
  <?php
  $scan = scandir('uploads');
  foreach ($scan as $file) {
    if (!is_dir($file)) {
      echo '<h3>' . $file . '</h3>';
      echo '<img src="uploads/' . $file . '" style="width: 400px;"/><br />';
    }
  }
  ?>
</body>

</html>
```

Fontes:

https://ionicframework.com/docs/native/camera
