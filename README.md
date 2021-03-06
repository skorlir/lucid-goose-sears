# Sears Admin Application

### Get started

`npm i && gulp serve`

### BEFORE DEPLOY

  1. Remove placeholder images and newsletter listings
     - In `src/jade/index.jade`, remove all the `.thumb`s and their `img` tags - these are placeholders
     - Also remove all the `+newsletterItem` calls that say 'EXAMPLE' in them
  2. Run `gulp build`
     - This will create the `public` directory
  3. Create directory structure on server like so:
     - ./server.js
     - ./public  (this is the public folder generated by build)
     - Essentially, public needs to be in the same folder as the folder in which the node process is run. That's probably going to be the same folder as the folder `server.js` is in.
     - I don't want to track public in git because it's redundant

### "Spec"

  1. Upload photos / newsletters (AWS S3)
  2. Select some number of photos to be "featured"
  3. Easily change between featured/non-featured
  4. Easily download newsletters
  5. Easily delete either of those things

### "Implementation"

  1. Upload button
     - Stops you from uploading non-photo files when in photos mode
     - Stops you from uploading non-pdf files when in newsletters mode
  2. Feature/unfeature images
     - Either by clicking on them or by dragging / dropping
  3. Deleting
     - Right click a photo and select delete to delete
     - Click the trash can next to a newsletter and then confirm to delete it
  4. Newsletter 'naming'
     - uses filename as name

### Possible Improvements

  - Optimistically load images before they are finally uploaded to S3 using `ObjectURL`s
  - Retry upload if fails
  - Show upload progress bar
  - Upload on drop in window, instead of just using drag and drop for arranging

## Backend Architecture

  - NODE_ENV credentials
    - AWS_ACCESS_KEY_ID
    - AWS_SECRET_ACCESS_KEY
    - AWS_S3_BUCKET (which is 'newport-academy')
    - BASIC_AUTH_USERNAME
    - BASIC_AUTH_PASSWORD
    - For local development, can use a .env file in the root directory of the application folder
  - On-disk cache (LevelDB)
    - Synchronizes with S3 on server start
    - Reduces number of requests made to S3
    - Just keeps track of URLs for accessing photos, and also keeps track of what photos are featured
  - On server init: read list of photos from s3
  - GET /photos => get list of uploaded images from local cache
  - GET /featured => cache.get('featured')
  - PUT /photos => upload image to s3 and update local cache
  - PUT /featured => cache.put('featured', ...)
  - GET /newsletters => gets list of uploaded newsletters from local cache
  - PUT /newsletters => upload newsletter pdf to s3 and update local cache

## Front-End

  - Photo listing
    - See design doc sketch mockup
    - "Featured" section
    - Easily feature/unfeature a photo
  - Newsletter listing
    - Download and delete buttons
  - Easily upload a new photo or newsletter

### Front-End Architecture

  - Vanilla JS
    - qwest for API calls
    - that's probably all you need
  - Pre-rendered jade, stylus
  - `gulp build`
  - `gulp serve`
  - Build, then serve
  - Use express.static(...) to put up the front-end
  - All 1 server (that was confusing last time I think)
