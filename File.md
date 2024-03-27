# P4 Compiler Documentation Site

**Date:** 21st March, 2024  
**Author:** Farhan Sadeek  
**Affiliation:** Stanford University & The Ohio State University  
**Email:** sadeek.1@osu.edu  
**Github profile:** [SadeekFarhan21](https://github.com/SadeekFarhan21) \
**Personal Website**: https://farhan-sadeek.netlify.app/ \
**Project Length**: 90 hours
**Mentors**:

## Motivation

Last year, when I was in high school, I got extremely interested in working with Arduino and Raspberry Pi. Together with me and my cousin, a network engineer, built a real-radar system with the microcontroller system. And that embarks the journey of working with embedded systems.

While I was browning through the list of organizations participating in GSoc, I made it my goal because it is one of the closest projects that I could work with. Along with that, I am currently working as a solutions engineer for Spectrum (NYSE:CHTR) and a Computer Science student at the Ohio State University. The first one taught me technical writing and the second gave me the knowledge to understand how computers and electronic devices work. And the best part about the project is that I can start writing anytime I can. Because of all of the above-mentioned reasons, I would like to be part of this program.

## Use case

Both p4lang and tutorials pages don’t have a well-organized documentation page which causes issues for beginners when looking for a one-stop solution for all the necessary information for them to get started. On top of that, it enhances collaboration and troubleshooting for further use cases even for experienced developers. So, creating well-formatted documentation is necessary for both experienced and beginner p4 learners.

This project aims to add more well-formatted documentation both on GitHub and a website. The website will be created using a Python framework called Sphinx allowing faster and easier rendering.

## Initial Research

- Emailed mentors about potential solutions for the documentation of the project.
- Browsed through the three recommended formats for the documentation site.
- Familiarized with the Sphinx framework for better workflow.
- Created framework/roadmap on the way of implementing this scalable documentation site.

## Goals

- Collect all the existing documentation and merge all of the documentation inside the docs folder.
- Inside the docs folder separate the files based on their type (getting started, install, tutorial, etc).
- Create and host a website using a Python Framework called Sphinx.

## Deliverables


The following are tehe deliverables for this project
- A well fromatted documentation website using Sphinx
- An updated documenation in Github for better understanding of the project

### Directory Structure
```
docs
│
├── _static
│   ├── css              - css file for custom formatting
│   └── img              - images for the website 
│
├── index.rst            - index of the website created by Sphinx
│
├── getting_started
│   ├── package_version  - installation from package version
│   │   ├── ubuntu.rst   - for ubuntu
│   │   └── debian.rst   - for debian
│   └── source           - installation from source
│       └── source.rst
│
├── dependencies        - dependencies across different OS
│   ├── ubuntu.rst
│   ├── fedora.rst
│   ├── macOS.rst
│   ├── garbage_collector.rst   - details about garbage collector
│   └── crash_dumps.rst         - details about crash dumps
│
├── development_tools           - developer tools
│   ├── docker.rst
│   └── bazel.rst
│
├── reference.rst
│
└── known_issues
    ├── frontend
    │   ├── common.rst
    │   ├── p4-14.rst
    │   ├── p4.rst
    │   └── parsers
    └── backend
    │   ├── bmv2.rst
    │   ├── dpdk.rst
    │   ├── ebpf.rst
    │   ├── graphs.rst
    │   ├── p4test.rst
    │   ├── 5c.rst
    │   └── ubpf.rst
    ├── specs
        └── specs.rst
```      
      
    
    




### Platform Abstractions

The API will use the unbuffered I/O functions provided by the platform to load files into memory. The methods of performing unbuffered I/O differ between platforms, so this module will abstract the platform specific mechanisms behind an object oriented interface, `FileAccessUnbuffered`.

This will be the only platform specific aspect of the module. I currently have access to windows and linux, and will concentrate on supporting these platforms. The abstraction for linux will use POSIX system calls functions to perform operations such as retrieving file size and reading or writing data to files. This may allow for easy implementation in OS X as well. The windows abstraction will use similar functions for this purpose. The `FileAccessUnbuffered` class will inherit from `FileAccess`, and will provide a template for platform abstractions of unbuffered file IO. This may be extended by providing abstractions for files streamed over the network as well.

### The API Interface

The module will be implemented as a server which will be issued commands through message channels. It will provide the `FileAccessCached` class which will also inherit from `FileAccess`. This class will be used to handle cached files with a specific caching policy, like `KEEP_FOREVER` or `LEAST_RECENTLY_USED_OUT` for example. 

The user (The developer of a game) will not be able to access the file immediately after attempting to load it as the required data will have to be cached first. Instead, the user will have to wait for the file to finish caching and then use it. This may be done by blocking the current thread until the file is cached, by repeatedly polling the file’s state to determine when it is loaded, or by providing a callback function to run once the operation is complete.

### The Cache

The cache is a large memory region which will be addressed using a page table with entries describing pages of a fixed size (4KB). This may be configurable since different architectures use different page sizes. A number of strategies may be used to manage the cache, such as FIFO, FILO and LRU. The user may select a caching policy appropriate to the situation. For example, if a file will be accessed in a strictly sequential manner, FIFO would be a good strategy to use, and would allow the implementation to perform optimisations such as reading ahead. The implementation could also allow for random access by loading pages on demand, though that would be slower and may require some form of synchronisation.

Another idea to be considered is the usage of multiple paged regions, each with a different page size. There could be three separate caches, one comprised of 1KB pages, another with 4KB pages, and yet another with 16KB pages. This allows for more optimisations to be done if the size of data read is more easily managed with pages of a different size. For example, a read whose size is not an exact multiple of 4KB could overflow into the 1KB region and not waste a 4KB page on a few hundred bytes of data. 

The intention is to interact with the cache through instances of the `FileAccessCached` class without exposing the user to the implementation. Statistics about memory usage may be queried from the server singleton. The `FileAcessCached` class will interact with the underlying media using the cache manager as an intermediary. The cache manager is not necessarily an interface to the file system alone and may also be used to cache data from, say, a network stream.

The cache manager will be multithreaded, and will handle multiple files parallelly.

### Integration With Godot Engine

Hooks will have to be attached to the asset loading code to make this happen. Logic must be written to synchronise loading of assets with dependencies as well.

### Further Possible Improvements 

There are other improvements we could make to the file IO mechanism, which may not be in the scope of the project but may be taken up later on.

- Using magic streams as detailed by this post, in conjunction with caching could vastly improve load times with fewer seeks.
- Using the cache with network streams.
- Abstract over archived files and multipart archives.

## Timeline

### Before 6th May:

- Get familiar with the p4c and tutorials code base.
- Get a good understanding of the problem.
- Set up the coding environment on a local computer.
- Familiarize myself with the specific type of writing for this project.
- Lurk and ask questions on Slack and the issues page.

### 6th May – 27th May:

- Interact with mentors.
- Review/revise the design.

### 27th May – 2nd June:

- Set up skeleton for cache server module.
- Implement a working custom Godot server to provide the API at runtime.
- Define all classes with stub methods.

### 2nd June – 20th June:

- Implement paging of memory in the cache.
- Implement cache management

### 10th July – 18th July:

- Interface with file system through `FileAccessUnbuffered`.
- Implement platform specific subclasses for `FileAccessUnbuffered`.
- Interface with engine asset import code.

### 18th July – 22nd July:

- Write documentation.
- Test & debug.

### 22nd July – 5th August:

- Test all code with unit and integration tests.
- Fix any remaining bugs.