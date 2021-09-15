---
title: "Hosting a local instance"
date: "2020-05-15"
---

To use the Viewer with locally generated BlobDir datasets, you will need to host a local instance of the Viewer. Convenience methods exist within BlobTools2 (see BlobTools 2 tutorial [_Opening a dataset in the Viewer_](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/opening-a-dataset-in-the-viewer/)) to start the Viewer with no additional configuration but more options are available when starting the Viewer directly. This page contains instructions for hosting a private locally hosted instance and is not intended to be used as guidance for hosting a publicly accessible instance.

The Viewer comprises two components, an Application Programming Interface (API) that loads a directory of BlobDir datasets and makes them available to the Viewer client, which is the portion that loads in the browser for interactive visualisation. Configuration options for both of these components can be set using environment variables or specified in a `.env` file, described in more detail below. The only option that must be modified from the default `.env.dist` file is the `BTK_FILE_PATH` variable which should be the path to a directory containing BlobDir datasets.

### Example

It is important to ensure that the same version of Node is used to run the Viewer as was used when running the `npm install` step (see [Install](https://blobtoolkit.genomehubs.org/install/) instructions). The Viewer API and client need to be started in separate terminals so you must ensure that the `btk_env` is activated in each terminal.

Start the Viewer API

```
$ conda activate btk_env
$ cd ~/blobtoolkit/viewer
$ cp .env.dist .env
$ BTK_FILE_PATH=~/BTK_TUTORIAL/DATASETS npm start
```

Or set `BTK_FILE_PATH` by editing the `.env` file:

```
$ nano .env
  ...
  BTK_FILE_PATH=~/BTK_TUTORIAL/DATASETS
  ...
$ npm start
```

Start the Viewer client in a new terminal window:

```
$ conda activate btk_env
$ cd ~/blobtoolkit/viewer
$ npm run client
```

Go to [http://localhost:8080/view/all](http://localhost:8080/view/all) in a web browser to start exploring your datasets.

If the Viewer is running on a remote server to which you have ssh access, datasets can be viewed interactively by forwarding ports 8000 (default API port) and 8080 (default client port) using a command similar to `ssh -L 8000:127.0.0.1:8000 -L 8080:127.0.0.1:8080 username@remote_host` before visiting the Viewer URL as above.

### Configuration

For a local instance, most variables can typically be left on their default settings. Variables that may be useful to set in your `.env` file are:

- `BTK_FILE_PATH` – The path of a directory containing BlobDir datasets. _\[**Required**\]_
- `BTK_CLIENT_PORT` – Port number for the Viewer client. _\[Default: 8080\]_
- `BTK_API_PORT` – Port number for the Viewer API. _\[Default: 8000\]_
- `BTK_DATASET_TABLE` – Display datasets in a table on main page. _\[Default: false\]_
- `BTK_USE_DEFAULT_LINKS` – Use default links to external resources. _\[Default: true\]_
- `BTK_CIRCLE_LIMIT` – Maximum number of records in a dataset for which 'no-hit' circles will be plotted. _\[Default: 100000\]_
- `BTK_NOHIT_THRESHOLD` – Maximum number of records in a dataset for which 'no-hit' data will be plotted. _\[Default: 1000000\]_

Additional variables if accessing the local instance via a name other than localhost:

- `BTK_HOST` – Hostname of computer the Viewer is running on. _\[Default: localhost\]_
- `BTK_ORIGINS` – CORS allowed origins for API connection. _\[Default: 'http://localhost:8080 http://localhost http://127.0.0.1:8080 http://127.0.0.1 null'\]_
