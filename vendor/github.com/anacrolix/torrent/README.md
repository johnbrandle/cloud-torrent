# torrent

[![CircleCI](https://circleci.com/gh/anacrolix/torrent.svg?style=shield)](https://circleci.com/gh/anacrolix/torrent)
[![GoDoc](https://godoc.org/github.com/anacrolix/torrent?status.svg)](https://godoc.org/github.com/anacrolix/torrent)
[![Join the chat at https://gitter.im/anacrolix/torrent](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/anacrolix/torrent?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This repository implements BitTorrent-related packages and command-line utilities in Go. The emphasis is on use as a library from other projects. It's been used 24/7 in production by a downstream, private service since late 2014.

There is support for protocol encryption, [DHT](https://github.com/anacrolix/dht), PEX, [uTP](https://github.com/anacrolix/utp), and various extensions. See the package documentation for a more complete list. There are several data storage backends provided: blob, file, and mmap, and you can write your own, such as to store data on S3, or in a database. You can use the provided binaries in `./cmd`, or use package `torrent` as a library for your own applications.

Many of the sub-packages can be used for other purposes: [bencode](https://godoc.org/github.com/anacrolix/torrent/bencode), and [tracker](https://godoc.org/github.com/anacrolix/torrent/tracker), in particular.

## Installation

Install the library package with `go get github.com/anacrolix/torrent`, or the provided cmds with `go get github.com/anacrolix/torrent/cmd/...`.

## Library example

There is a small example in the [package documentation](https://godoc.org/github.com/anacrolix/torrent).

## Other public projects using torrent

 * [Go Peerflix](https://github.com/Sioro-Neoku/go-peerflix)
 * [Cloud Torrent](https://github.com/johnbrandle/cloud-torrent)
 * [Android Torrent Client](https://gitlab.com/axet/android-torrent-client)
 * [Android libtorrent](https://gitlab.com/axet/libtorrent)
 * [Trickl - Torrent Client](https://play.google.com/store/apps/details?id=com.shwifty.tex)
 * [Confluence](https://github.com/anacrolix/confluence)

## Commands

Here I'll describe what some of the provided commands in `./cmd` do.

Note that the [`godo`](https://github.com/anacrolix/godo) command which is invoked in the following examples builds and executes a Go import path, like `go run`. It's easier to use this convention than to spell out the install/invoke cycle for every single example.

### torrent

Downloads torrents from the command-line. This first example does not use `godo`.

	$ go get github.com/anacrolix/torrent/cmd/torrent
    # Now 'torrent' should be in $GOPATH/bin, which should be in $PATH.
	$ torrent 'magnet:?xt=urn:btih:KRWPCX3SJUM4IMM4YF5RPHL6ANPYTQPU'
    ubuntu-14.04.2-desktop-amd64.iso [===================================================================>]  99% downloading (1.0 GB/1.0 GB)
    2015/04/01 02:08:20 main.go:137: downloaded ALL the torrents
    $ md5sum ubuntu-14.04.2-desktop-amd64.iso
    1b305d585b1918f297164add46784116  ubuntu-14.04.2-desktop-amd64.iso
    $ echo such amaze
    wow

### torrentfs

torrentfs mounts a FUSE filesystem at `-mountDir`. The contents are the torrents described by the torrent files and magnet links at `-metainfoDir`. Data for read requests is fetched only as required from the torrent network, and stored at `-downloadDir`.

    $ mkdir mnt torrents
    $ godo github.com/anacrolix/torrent/cmd/torrentfs -mountDir=mnt -metainfoDir=torrents &
    $ cd torrents
    $ wget http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-desktop-amd64.iso.torrent
    $ cd ..
    $ ls mnt
    ubuntu-14.04.2-desktop-amd64.iso
    $ pv mnt/ubuntu-14.04.2-desktop-amd64.iso | md5sum
    996MB 0:04:40 [3.55MB/s] [========================================>] 100%
    1b305d585b1918f297164add46784116  -

### torrent-magnet

Creates a magnet link from a torrent file. Note the extracted trackers, display name, and info hash.

    $ godo github.com/anacrolix/torrent/cmd/torrent-magnet < ubuntu-14.04.2-desktop-amd64.iso.torrent
	magnet:?xt=urn:btih:546cf15f724d19c4319cc17b179d7e035f89c1f4&dn=ubuntu-14.04.2-desktop-amd64.iso&tr=http%3A%2F%2Ftorrent.ubuntu.com%3A6969%2Fannounce&tr=http%3A%2F%2Fipv6.torrent.ubuntu.com%3A6969%2Fannounce
