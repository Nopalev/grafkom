# Penjelasan Tugas Texture Mapping

Source code yg digunakan adalah pengembangan dari source code yang telah diberikan pada classroom.

Pada source code contoh, terdapat potongan kode berikut:

```js
let img = new Image();
img.onload = function () {
    textureObjects[0] = gl.createTexture();
    gl.bindTexture(gl.TEXTURE_2D, textureObjects[0]);
    gl.texImage2D(
        gl.TEXTURE_2D,
        0,
        gl.RGBA,
        gl.RGBA,
        gl.UNSIGNED_BYTE,
        img
    );
    gl.generateMipmap(gl.TEXTURE_2D);
    console.log("loaded texture", 0);
    draw();
};
img.src = textureURLs[0];
```

Potongan kode diatas digunakan untuk mengubah gambar menjadi bentuk html image untuk nantinya ditempelkan pada kubus. karena terdapat 6 sisi kubus dan 6 gambar yang akan digunakan, maka proses ini seharusnya diulangi sebanyak 6 kali. Berikut adalah implementasi dari saya:

```js
for (let i = 0; i < 6; i++) {
    img[i] = new Image();
    img[i].onload = function () {
        textureObjects[i] = gl.createTexture();
        gl.bindTexture(gl.TEXTURE_2D, textureObjects[i]);
        gl.texImage2D(
            gl.TEXTURE_2D,
            0,
            gl.RGBA,
            gl.RGBA,
            gl.UNSIGNED_BYTE,
            img[i]
        );
        gl.generateMipmap(gl.TEXTURE_2D);
        if (i === 5) draw();
    };
    img[i].src = textureURLs[i];
}
```

Pada kode diatas, saya mengubah img menjadi array of img, dan memanggil fungsi draw pada akhir iterasi (karena hanya cukup dipanggil sekali saja).

Perubahan berikutnya terdapat pada fungsi draw. Pada contoh, terdapat potongan kode berikut pada fungsi draw:

```js
// Save the viewing transformation.
let saveMV = mat4.clone(modelview);

drawSquare(textureObjects[0]); // front face
```

ke-enam dari isi array `textureObjects` juga harus digambar. Namun, agar gambar dapat ditempel pada kubus sesuai dengan posisi seharusnya, maka harus dilakukan rotasi sebelum penempelan tiap gambar. Adapun variabel `saveMV` dapat digunakan untuk mengembalikan modelview menjadi posisi semula dengan potongan kode berikut

```js
mat4.copy(modelview, saveMV);
```

Hal ini dapat berguna ketika akan menempelkan gambar atas dan bawah agar pemasangan gambar sesuai.

Namun, saya menggunakan pendekatan lain, yaitu melakukan perputaran sebanyak 360 derajat, baru melakukan rotasi yang akan digunakan untuk menempelkan gambar atas dan bawah, sehingga saya tidak menggunakan variabel `saveMV`. Berikut adalah potongan kode saya pada fungsi draw:

```js
let index = 0;

drawSquare(textureObjects[index++]); // front face

mat4.rotateY(modelview, modelview, Math.PI / 2);
drawSquare(textureObjects[index++]); // right face

mat4.rotateY(modelview, modelview, Math.PI / 2);
drawSquare(textureObjects[index++]); // back face

mat4.rotateY(modelview, modelview, Math.PI / 2);
drawSquare(textureObjects[index++]); // left face

mat4.rotateY(modelview, modelview, Math.PI / 2); // 360 full rotate

mat4.rotateX(modelview, modelview, -Math.PI / 2);
drawSquare(textureObjects[index++]); // up face

mat4.rotateX(modelview, modelview, Math.PI);
drawSquare(textureObjects[index++]); // bottom face
```

Sekian penjelasan dari saya, Terimakasih banyak untuk ilmu yang telah diberikan.
