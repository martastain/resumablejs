resumable.js
============

Resumable is a JavaScript library providing stable and resumable uploads via the HTML5 File API.
It does not have any external dependencies other the HTML5 File API.
This is relied on for the ability to chunk files into smaller pieces.

This fork should be backwards compatible with the original version (apart from different default values),
but also provides compatibility with [NGINX resumable upload specification](http://www.grid.net.ru/nginx/resumable_uploads.en.html).



Usage
-----

A new Resumable object is created with information of what and where to post:

```javascript
var r = new Resumable({
  target:'/upload',
  query:{upload_token:'my_token'}
});
```

To allow files to be either selected and drag-dropped, you’ll assign drop target and a DOM item to be clicked for browsing:

```javascript
r.assignBrowse(document.getElementById('browseButton'));
r.assignDrop(document.getElementById('dropTarget'));
```

### NGINX configuration

NGINX must be compiled with [nginx-upload-module](https://github.com/fdintino/nginx-upload-module/).

```nginx
location /upload {
	client_max_body_size    100M;
	auth_request            /upload_auth;

	upload_resumable        on;

	upload_store            /data/upload;
	upload_state_store      /data/state;
	upload_pass             /api/upload;
	upload_pass_args        on;

	upload_set_form_field   name         "$upload_file_name";
	upload_set_form_field   content_type "$upload_content_type";
	upload_set_form_field   path         "$upload_tmp_path";

	upload_cleanup          400 404 499 500-505;
}
```
