# ZipStream - Compressed file sharing web app
Upload, compress, save, share, download, unlist, encrypt, decrypt and search for zip folders.

# Distinctiveness and Complexity
I believe my project satisfies the criteria of distinctiveness and complexity because it explores subjects the course did not touch upon, such as multiple files uploading, downloading files using javascript requests, drag and drop with animations, zipping files and UUID (as a substitute for incremental IDs). UUID is used instead of incremental IDs so unlisted zip URLs won't be guessed easily. A custom context processor was added, allowing the security integer value choices to be exposed easily in templates. A custom template filter was also created, to easily convert integer number of bytes to a string readable amount of bytes ('1000' to '1KB'). Zipping is used mainly to compress and lower disk usage on the server, and done by the builtin 'zipFile' python module, same for unzipping and gathering information about files inside. I believe those additions and the concept of a file compressing & sharing web app is distinct enough from the rest of the course projects. 

# How it works
## Accounts
The top bar has navigation buttons to log in, log out, register.

## Searching and Downloading
On the initial index page (aka 'search' page), users can recieve from the server all the zips that they are allowed to view (their own, or other's public files).<br>
This is given via paging, and viewed as a table, sorted by date.<br>
Users can use the search bar to search for a specific zip folder, which sends a request to the server and recives back zips with names contains the search.<br>
Unlisted zips can only be viewed through given URL, and won't be shown on the initial page or via search.<br>
Clicking on a zip folder item, will take you the zip folder page, displaying all the files inside and their size. Here you can also download the zip file.<br>
Owners can change the security of the zip file (public, private, unlisted) without refreshing the page.<br>
If the zip file is unlisted or public, there will be a button to copy the link into the clipboard.<br>
You can click the owner's name to enter the profile page, displaying only zips uploaded by the profiled user.<br>
Here users can also use the search bar.<br>

## Uploading and Zipping
If the user is signed in, an 'Upload' navigation button will be at the top, which will take the user to the upload page.<br>
The user inputs files (folders and subfolders are not supported) into the form, by drag and dropping or browsing. Multiple files can be dropped or selected.<br>
If the user reached the upload limit, a warning will be displayed and he will not be able to upload the form until the files are smaller than the upload limit.<br>
Name for the zip is required, description is optional.<br>
Security: Public - Anyone can view. Unlisted - can only be viewed with the link, unless you are the owner. private - only viewed by the owner.<br>
Than, the form can uploaded to the django server.<br>
The server recieves the form and creates ZipUpload object accordingly.<br>
Using Python's built in 'zipFile' module, the server zips the files and puts them in the 'zips' folder, with the ZipUpload uuid as the zip name, and saves it using FileField.<br>
Later the zip can requested using the uuid, and the server can retrieve the zip file using the FileField and be unzipped using the same module, exposing the information about files inside, and sending them to the user.<br>

## Files

### Python
- [filters.py:](zipstream/templatetags/filters.py) Custom template filters. Mainly to convert byte count to human readable byte count.
- [context_processors.py:](zipstream/templatetags/context_processors.py) Additional context processor for security option values.
- [views.py:](zipstream/views.py) Views, handling HTTP requests.
- [urls.py:](zipstream/urls.py) List of the site URLs and the link to their views function.
- [models.py:](zipstream/models.py) Models and their methods, as well as zipping methods. ZipUpload model can be found here.

#### Javascript
- [file-drag-drop.js:](zipstream/static/zipstream/file-drag-drop.js) Handles file drag and dropping and inputting via click. Updates the UI, listing the files and checks for file sizes, displaying a warning if reached max upload size.
- [search.js:](zipstream/static/zipstream/search.js) Handles search input by clicking enter or the search icon in both 'profile' page and 'index' page.
- [zip.js:](zipstream/static/zipstream/zip.js) Handles various actions on the 'zip' page, such as changing security, downloading the zip, and deleting it.
- [topbar.js:](zipstream/static/zipstream/topbar.js) Resizes the hidden div according to the top bar size. The hidden div fills the area of the top bar, so it could stay in 'fixed' display while not overlapping anything, unless scrolling down.

#### CSS
- [styles.css:](zipstream/static/zipstream/styles.css) CSS for some animations and specific elements.
- Note: most elements use bootstrap classes rather than this project's styles.css file.

#### <a name="pages"></a>HTMLs & Pages
- [layout.html:](zipstream/templates/zipstream/layout.html) 
    - Basic layout with navigation bar and paging.
    - All other HTMLs derive from layout.html, except for profile.html which derives from index.html.
    - if logged out, the navigation bar will display links to login, register and search (index page).
    - if logged in, the navigation bar will display links to logout, upload, user's profile and search (index page).
    - clicking on the logo will link to index page.

- [index.html '/':](zipstream/templates/zipstream/index.html) 
    - view all public zips and your zips as a table.
    - the table displays information about the owner, download count, size and name of each zip on the page.
    - pagination of 10 zips for each page.
    - input search and click the icon or press enter to search through public and your zips, using names and descriptions.

- [profile.html '/profile/(user id)'](zipstream/templates/zipstream/profile.html) 
    - view all public zips of the profiled user. If you are the profiled user, you can see private and unlisted files as well.
    - the total number of zips you can view that the user has upload. 
    - pagination of 10 zips for each page.
    - input search and click the icon or press enter to search through public and your zips, using names and descriptions.
    - inherits from index.html.

- [upload.html '/upload/'](zipstream/templates/zipstream/upload.html)
    - Here you can choose all the details of the zipped directory, and upload it with its files.
    - Name
    - Security: public, unlisted (others can access via URL only), private (owner only)
    - Files, you can click browse and select multiple, or drag and drop files. Entering files deletes previous entered files. Folders are not supported.
    - Optional description.
    - 10 MB file upload size limit.
    The limit is checked on both the server via python and user's machine via JS, displaying an error in case the user passed the limit. 

- [zip.html '/zip/(zip uuid)'](zipstream/templates/zipstream/upload.html)
    - view all files inside the zip with the uuid.
    - view total downloads, name, description, security measure.
    - download the zip.
    - owner can change security option without reloading the page.
    - owner can delete the zip.
    - A button to copy the link. This is mostly for 'unlisted' zips, as the only way to share them is through links. This button is hidden on private zips, as they can't be shared with other users.
    - click on the owner's name to view his profile.

- [register.html '/register'](zipstream/templates/zipstream/register.html)
    - standard page to register.

- [login.html '/login'](zipstream/templates/zipstream/login.html)
    - standard page to login.

- '/logout'
    - standard URL to logout.

## How to run
The same way you'd run other projects in this course:<br>
Open root folder in CMD, and run ```python manage.py runserver```<br>
Open the URL displayed in console.<br>
You can now register, upload files, search, download and share zips with other users.<br>
For an explanation how to use and what each page does, read [HTMLs & Pages](#pages).

## Changes to [settings.py](capstone/settings.py)
- [ZIPS_DIR](zips/) variable for the zips folder path was added. This is where files are zipped and saved to as a zip. 
- 'zipstream.templatetags.context_processors.processors' was added under TEMPLATES > OPTIONS > context_processors.
 
## Compatibility
- upload form adjusted for mid-thin size screens (< 550px ) and fills the width accordingly.
- Top navigation bar being resized for smaller screen ( < 355px ).
- The div behind the navigation bar is being resized accordingly using javascript.
- On very thin screens ( < 355px ) the naviagtion labels are hidden (visible icons only) for extra space.
