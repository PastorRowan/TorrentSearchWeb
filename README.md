
# TorrentSearchWeb

A lightweight, client-side web application for searching BitTorrent metadata and opening torrent results as magnet links.

TorrentSearchWeb provides a simple search interface that queries The Pirate Bay API, displays torrent information, generates a magnet URI using the result's info hash and a list of trackers, and attempts to open the magnet link using the user's installed BitTorrent client.

## Features

- Search for torrents using a simple search bar.
- Query The Pirate Bay API for search results.
- Display torrent:
    - Name
    - Size
    - Seeders
    - Leechers
- Generate magnet links from torrent info hashes.
- Automatically add trackers from the ngosang/trackerslist project.
- Validate tracker URLs before adding them.
- Open magnet links using the operating system's registered BitTorrent application.
- Detect whether the browser supports the required custom-protocol handling.
- Display formatted file sizes using binary units (KiB-style calculation with 1024 bytes per unit).
- No backend server is required.

## How It Works

When the user presses Enter in the search field, the application sends a request to all torrent search providers

The response is validated before being converted into the application's internal result format:
```
{
    name,
    infoHash,
    size,
    leechers,
    seeders
}
```

The results are then inserted into the results table.

## Magnet Links

A magnet URI is generated from the torrent's info hash:
```
magnet:?xt=urn:btih:<info-hash>
```

Trackers are also appended using the tr parameter.

For example:
```
magnet:?xt=urn:btih:<info-hash>&tr=<tracker>&tr=<tracker>
```

Clicking a search result attempts to open the generated magnet link.

## Tracker Management

TorrentSearchWeb starts with a built-in list of trackers and then attempts to retrieve the latest tracker list from:
```
https://raw.githubusercontent.com/ngosang/trackerslist/master/trackers_best.txt
```

Only trackers using the following protocols are accepted:
- http
- https
- udp

The tracker path must also be:
```
/announce
```

Duplicate trackers are ignored.

If retrieving the external tracker list fails, the application continues using the built-in trackers.

## Browser Compatibility

Magnet links are handled through the browser's custom-protocol mechanism.

The project uses [custom-protocol-detection](https://github.com/ismailhabib/custom-protocol-detection) to determine whether the browser can detect applications registered to handle the magnet protocol.

1. The actual ability to open the magnet link depends on:
2. The browser supporting the required protocol behaviour.
3. The operating system allowing the magnet: protocol to be invoked.
4. A BitTorrent client being installed.
5. The BitTorrent client being registered as a handler for magnet links.

TorrentSearchWeb does not download the torrent itself. It hands the magnet URI to the operating system and lets the registered BitTorrent client handle it.

## Interface

The interface consists of:

- A search input.
- A results table.

## The results table contains:

| Name | Size | Seeders | Leechers |
| -------- | -------- | -------- | -------- |
| Torrent name | Formatted torrent size | Number of seeders reported by the API | Number of leechers reported by the API |

Clicking a result immediately attempts to open its magnet link.

## Dependencies

### Materialize CSS

The interface uses [Materialize CSS](https://materializeweb.com) 2.2.2 for its UI components and styling.

The compiled Materialize stylesheet is included directly in the HTML file.

### custom-protocol-detection

The project uses:

[custom-protocol-detection](https://github.com/ismailhabib/custom-protocol-detection)

to detect whether the browser can handle custom application protocols such as **magnet:**.

## External Services

The application currently depends on:
- [The Pirate Bay API](https://github.com/PastorRowan/ThePirateBayAPI) - torrent search results.
- [ngosang/trackerslist](https://github.com/ngosang/trackerslist)

Because these services are external, their availability is outside the control of [TorrentSearchWeb](https://github.com/PastorRowan/TorrentSearchWeb).

## Running the Project

TorrentSearchWeb is a static web application and does not require a build system or backend.

The project can be served using any static HTTP server.

For example, with Python:
```
python -m http.server 8000
```
Then open:
```
http://localhost:8000
```

You can also host the project using a static hosting service.

## Project Structure

```
TorrentSearchWeb/
├── .gitignore                   # Git ignore rules
├── LICENSE                      # Project license
├── README.md                    # Project documentation
├── ThePirateBayResponse.json    # Example response returned by the The Pirate Bay API
└── TorrentSearchWeb.html        # Main application containing the HTML, CSS, and JavaScript
```

The project is currently implemented as a single HTML document:
```
HTML
├── Page structure
├── Search interface
└── Results table

CSS
├── Materialize CSS
└── Custom application styling

JavaScript
├── Magnet URI generation
├── Tracker management
├── Tracker validation
├── Magnet protocol handling
├── File-size formatting
├── Torrent searching
└── Result rendering
```

## Security Considerations

TorrentSearchWeb does not download torrent content itself. It generates magnet URIs from search results and passes those URIs to the user's installed BitTorrent client.

Users should independently verify torrent content and only download material they are legally permitted to obtain.

## Legal Notice

TorrentSearchWeb is a search and magnet-link launching interface. It does not host or distribute torrent files itself.

The availability and legality of content returned by third-party torrent indexes varies by jurisdiction. Users are responsible for ensuring that their use of the application and any content they download complies with applicable laws and the rights of content owners.

## License

See the [LICENSE](LICENSE) file for the complete license text.

Copyright © 2026 PastorRowan
