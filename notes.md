# 11 TEXTURES 
## WHAT ARE TEXTURES?
* Images that will cover the surface of the geometries 
* Many types with many different effects
* Not just about adding a colour to a geometry
### COLOR (OR ALBEDO)
* most simple one
* applied on the geometry
### ALPHA
* greyscale image 
* white visible
* black not visible
### HEIGHT (OR DISPLACEMENT)
* greyscale image
* move the vertices to create some relief
* need enough subdivision 
### NORMAL
* adds details (light)
* don't need subdivision
* the vertices won't move 
* lure the light about the face orientation 
* better performances than adding a height texture with a lot of subdivision
### AMBIENT OCCLUSION
* greyscale image
* adds fake shadows in crevices
* not physically accurate
* helps to create constrast and see details
### METALNESS
* greyscale image
* white is metallic
* black is not metallic
* mostly used for reflection 
### ROUGHNESS
* greyscale 
* in duo with metalness
* white is rough
* black is smooth
* mostly for light dissipation
##
* many other types but above are the most used
* can also create own texture
* these textures follow the PBR principes
### PBR 
* Physically Based Rendering
* many techniques that tend to follow real-life diretions to get realistic renders
* becoming the standard for realistic renders
* many software, engines, and libraries are using it
## HOW TO LOAD TEXTURES
### GETTING THE URL OF IMAGE
* to load texture, URL of image file needed
* 2 ways of doing it:
    1. put image texture in the `/arc/` folder and import 
    2. Put imae in `/static/` folder and access it directly 
        ```
        const inputSource = '/image.png'
        console.log(imageSource)
        ```
### LOADING THE IMAGE 
* load textures located in `/static/`
### USING NATIVE JAVASCRIPT
* create an `image` instance, listen to the `load` event, and change its `src`:
    ```
    const image = new Image()
    image.onload = () => {
        console.log('image loaded')
    }
    image.src = 'textures/door/color.jpg'
    ```
* we can't use that image directly b/c it needs to be transformed into a `Texture` first
* `Texture` is Three.js and a class 
* create a `texture` variable with the `Texture` class:
    ```
    const image = new Image()
    image.addEventListener('load', () => {
        const texture = new THREE.Texture(image)
    })
    image.src = '/textures/door/color.jpg'
    ```
* need to use that `texture` in the `material`
* `texture` variable has been declared in a function and can't access it from outside the function 
* create the texture outside the function and update it once the image is loaded with `needsUpdate = true`:
    ```
    const image = new Image()
    const texture = new THREE.Texture(image)
    image.addEventListener('load', () => {
        texture.needsUpdate = true
    })
    image.src = '/textures/door/image.jpg'
    ```
* replace the `color` property by `map` and use the `texture`
    ```
    const material = new THREE.MeshBasicMaterials({ maps: texture })
    ```
### USING TEXTURELOADER
* instantiate a variable using the `TextureLoader` class and use its `.load(...)` method to create a texture:
    ```
    const textureLoader = new THREE.TextureLoader()
    const texture - textureLoader.load('/textures/door/color.jpg')
    ```
* one `TextureLoader` can load multiple textures
* send 3 functions after the path:
    1. `load` - image loaded successfully 
    2. `progress` - loading is progressing
    3. `error` - something went wrong 
    ```
    const textureLoader = new THREE.TextureLoader()
    const texture = textureLoader.load('/textures/door/color.jpg',
        () => {
            console.log(`load`)
        },
        () => {
            console.log('progress')
        },
        () => {
            console.log('error')
        }
    )
    ```
### USING LOADINGMANAGER
* can use `LoadingManager` to mutulise the events
* useful to know the global loading progress or be informed when everything is loaded 
* useful when loading multiple textures 
* create an instance of `LoadingManager` class and pass it to the `TextureLoader`:
    ```
    const loadingManager = new THREE.LoadingManager()
    const textureLoader = new THREE.TextureLoader(loadingManager)
    ```
* listen to the events by replacing the following properties by your own functions:
    ```
    const loadingManager = new THREE.LoadingManager()

    loadingManager.onStart = () => {
        console.log('onStart')
    }
    loadingManager.onLoaded = () => {
        console.log('onLoaded')
    }
    loadingManager.onProgress = () => {
        console.log('onProgress')
    }
    loadingManager.onError = () => {
        console.log('onError')
    }

    const textureLoader = new THREE.TextureLoader(loadingManager)
    const texture = textureLoader.load('/textures/door/color.jpg')
    ```
* load more images: 
    ```
    const textureLoader = new THREE.TextureLoader(loadingManager)
    const colorTexture = textureLoader.load('/textures/door/color.jpg')
    const alphaTexture = textureLoader.load('/textures/door/alpha.jpg')
    const heightTexture = textureLoader.load('/textures/door/height.jpg')
    const normalTexture = textureLoader.load('/textures/door/normal.jpg')
    const ambientOcclusionTexture = textureLoader.load('/textures/door/ambientOcclusion.jpg')
    const metalnessTexture = textureLoader.load('/textures/door/metalness.jpg')
    const roughnessTexture = textureLoader.load('/textures/door/roughness.jpg')
    ```
## UV UNWRAPPING
* replace the `BoxGeometry` with other geometries:
    ```
    const geometry = new THREE.SphereBufferGeometry(1, 32, 32)
    ```
* texture is being streched/squeezed in different way to cover the geometry
* this is called `UV unwrapping` and it's like unwrapping origami to make it flat 
* each vertex will have a 2D coordinate on a flat plane (usually a square)
* revert back to `BoxGeometry` 
* can see UV coordinates in `geometry.attributes.uv`
    ```
    console.log(geometry.attributes.uv)
    ```
* these UV coordinates are generated by Three.js
* if you create you're own geometry you'll have to specify the UV coordinates
* if you're making the geometry using a 3D software, you'll also have to do the UV unwrapping 
## TRANSFORMING THE TEXTURE
* repeat the texture by using the `repeat` property
* it'a a `Vector2` with `x` and `y` properties 
    ```
    const colorTexture = textureLoader.load('/textures/door/color.jpg)
    colorTexture.repeat.x = 2 
    colorTexture.repeat.y = 3
    ```
* by default the texture doesn't repeat and the last pixel gets stretched
* can change this by using `THREE.RepeatWrapping` on the `wrapS` and `wrapT` properties:
    ```
    colorTexture.wrapS = THREE.RepeatWrapping
    colorTexture.WrapT = THREE.RepeatWrapping
    ```
* can alternate the direction with `THREE.MirroredRepeatWrapping`:
    ```
    colorTexture.wrapS = THREE.MirroredRepeatWrapping
    colorTexture.wrapT = THREE.MirroredRepeatWrapping
    ```
### OFSET
* can offset the texture using `offset` property which is a `Vector2`:
    ``` 
    colorTexture.offset.x = 0.5
    colorTexture.offset.y = 0.5
    ```
### ROTATION 
* rotate texture using `rotate` property:
    ```
    colorTexture.rotation = Math.PI * 0.25
    ```
* in radians 
* remove the `offset` and `repeat` properties to see that the rotation occurs around the bottom left corner, this is the `0, 0` UV coordinates 
* can change the pivot point with the `center` property which is a `Vector2`
    ```
    colorTexture.rotation = Math.PI * 0.25
    colorTexture.center.x = 0.5
    colorTexture.center.y = 0.5
    ```
## FILTERING AND MIPMAPPING
* look at the cube's top face while this face is almost hidden, you can see a blurry texture due to the filerting a mipmapping
### MIPMAPPING
* `Mipmapping` is a technique that consists of creating half a smaller version of a texture again and again until we get a `1x1` texture
* all those texture variations are sent to the GPU and the GPU will choose the most appropriate version of the texture
* all of this is already handled by Three.js and the GPU but we can choose different algorithms 
* there are 2 types of filter algoriths:
### MINIFICATION FILTER
* happens when the pixels of the texture are smaller than the pixels of the render (texture is too big for the surface it covers)
* can change the minificatioon filter of the texture  using `minFilter` property with these 6 values:
    - `THREE.NearestFilter`
    -  `THREE.LinearFilter`
    - `THREE.NearestMipmapNearestFilter`
    - `THREE.NearestMipmapLinearFilter`
    - `THREE.LinearMipmapNearestFilter`
    - `THREE.LinearMipmapLinearFilter` (default)
* difference between each is very small 
* can be used if you are not happy with blurriness of texture 
* try each one to get result you want 
* when zoomed out on top face of cube, to get the sharpest image use:
    ```
    colorTexture.minFilter = THREE.NearestFilter
    ```
* test with `checkerboard-1024x1024.png` to see the glitchy artefacts more clearly:
    ``` 
    const colorTexture = textureLoader.load('/textures/checkerboard-1024x1024.png')
    ```
* these artefacts are called `moiré patterns`
### MAGNIFICATION FILTER 
* happens when the pixels of the texture are bigger than the pixels of the render, i.e. the texture is too small for the surface it covers
* use the `checkerboard-8x8.png` texture
    ```
    const colorTexture = textureLoader.load('/textures/checkerboard-8x8.png')
    ```
* it looks bad but depends on the context
* if the effect isn't too exaggerated, the user probably won't notice
* can change the magnification filter on the texture using the `mapFilter` property with these 2 values:
    - `THREE.NearestFilter`
    - `THREE.LinearFilder` (default)
* using the following makes the image alot sharper:
    ```
    colorTexture.magFilter = THREE.NearestFilter
    ```
* using `THREE.NearestFilter` is better than the others for performance 
* if using `THREE.NearestFilter` on `minFilter`, don't need mipmaps 
    * deactivate mipmaps generation with `colorTexture.generateMipmaps = false`
    ```
    colorTexture.generateMipmaps = false
    colorTexture.minFilter = THREE.NearestFilter
    ```
## TEXTURE FORMAT AND OPTIMISATION 
* when preparing textures, keep in mind 3 crucial elements 
    - weight 
    - size (or resolution)
    - data 
### THE WEIGHT 
* users have to download the textures 
* chose the right type of file: 
    - `.jpg` - lossy compression but usually lights 
    - `.png` - lossless compression but usually heavier 
* can use compression websites and softwares like `TinyPNG`
### THE SIZE 
* each pixel of the texture will have to be stored on the GPU regardless of the images weight 
* GPU has storage limitations 
* worse b/c mipmapping increases the number of pixels to store 
* try to reduce the size of images as much as possible 
* mipmapping will produce half smaller version of the texture repeatedly until `1x1`
* b/c of this texture width and height must be a power of 2 
    - `512x512`
    - `1024x1024`
    - `512x2018`
### THE DATA 
* textures support transparency but we can't have transparency in `.jpg` 
* if you want to have only one texture that combine color and alpha better to use `.png` file 
* if using a `normal` texture we need the exact values, so don't want to apply lossy compression and better to use `.png`
* sometimes can combine different data into one texture by using the red, green, blue, and alpha channels seperatly 
* difficulty is to find the right combinations of texture formats and resolutions 
    * need to spend time on this otherwise performance issues can occur
## WHERE TO FIND TEXTURES 
* always hard to find perfect textures 
* good sites to search:
    - `poliigon.com`
    - `3dtextures.me`
    - `arroway-textures.ch` 
- always make sure you have the right to use the texture if it's not for personal usage 
- can also create you own textures with photos and 2d softwares like photoshop or use procedural textures with `Substance Designer`






