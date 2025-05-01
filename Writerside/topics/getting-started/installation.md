# Installation

Boson brings together a set of components, each handling a key part of the system:

- [**Runtime**](https://github.com/BosonPHP/Runtime) — acts as the bridge between your PHP code 
  and the underlying operating system. This is the main API that you can use both in runtime and 
  during development.

- [**Frontend**](https://github.com/BosonPHP/Frontend) – written in C/C++, it offers a cross-platform 
  low-level API (ABI) to interact with the graphics subsystem.

- <tooltip term="TODO">**Backend** – powers the execution of your PHP code, making sure everything 
  runs smoothly behind the scenes.</tooltip>

Together, they create a seamless environment for building rich desktop applications with 
web technologies — without the bloat.

## Requirements

<tabs>
    <tab title="Development">
        List of dependencies required for development.
        <tip>
        The best development experience for Boson is to 
        have PHP running on your development machine directly.
        </tip>
        <tabs>
            <tab title="Windows">
                <list>
                    <li>Windows 10+</li>
                    <li>x86 or Amd64</li>
                    <li>PHP 8.4+
                        <list>
                            <li>An <code>ext-ffi</code> extension</li>
                        </list>
                    </li>
                </list>
            </tab>
            <tab title="MacOS">
                <list>
                    <li>MacOS 12+</li>
                    <li>x86, Amd64 or Arm64</li>
                    <li>PHP 8.4+
                        <list>
                            <li>An <code>ext-ffi</code> extension</li>
                        </list>
                    </li>
                </list>
            </tab>
            <tab title="Linux">
                <list>
                    <li>Linux</li>
                    <li>x86, Amd64 or Arm64</li>
                    <li>PHP 8.4+
                        <list>
                            <li>An <code>ext-ffi</code> extension</li>
                        </list>
                    </li>
                    <li>Frontend dependencies (one of):
                        <tabs>
                        <tab title="GTK 4">
                            <list>
                                <li><code>gtk4</code></li>
                                <li><code>webkitgtk-6.0</code></li>
                            </list>
                        </tab>
                        <tab title="QT 5">
                            <list>
                                <li><code>qt5-qtwebengine</code></li>
                                <li><code>qt5-qtwebchannel</code></li>
                                <li><code>qt5-qtbase-gui</code></li>
                                <li><code>qt5-qtbase</code></li>
                            </list>
                        </tab>
                        <tab title="QT6">
                            <list>
                                <li><code>qt6-qtwebengine</code></li>
                                <li><code>qt6-qtwebchannel</code></li>
                                <li><code>qt6-qtbase-gui</code></li>
                                <li><code>qt6-qtbase</code></li>
                            </list>
                        </tab>
                        </tabs>
                    </li>
                </list>
            </tab>
        </tabs>
    </tab>
    <tab title="Production">
        List of dependencies required for build target.
        <tip>
        PHP is not required on the target platform, as it is 
        supplied with the ready-to-distribute application.
        </tip>
        <warning>
        The project is in development, soThe project is in development, so 
        list of dependencies may change in the future.
        </warning>
        <tabs>
            <tab title="Windows">
                <list>
                    <li>Windows 10+</li>
                    <li>x86 or Amd64</li>
                </list>
            </tab>
            <tab title="MacOS">
                <list>
                    <li>MacOS 12+</li>
                    <li>x86, Amd64 or Arm64</li>
                </list>
            </tab>
            <tab title="Linux">
                <list>
                    <li>Linux</li>
                    <li>x86, Amd64 or Arm64</li>
                    <li>Frontend dependencies (one of):
                        <tabs>
                        <tab title="GTK 4">
                            <list>
                                <li><code>gtk4</code></li>
                                <li><code>webkitgtk-6.0</code></li>
                            </list>
                        </tab>
                        <tab title="QT 5">
                            <list>
                                <li><code>qt5-qtwebengine</code></li>
                                <li><code>qt5-qtwebchannel</code></li>
                                <li><code>qt5-qtbase-gui</code></li>
                                <li><code>qt5-qtbase</code></li>
                            </list>
                        </tab>
                        <tab title="QT6">
                            <list>
                                <li><code>qt6-qtwebengine</code></li>
                                <li><code>qt6-qtwebchannel</code></li>
                                <li><code>qt6-qtbase-gui</code></li>
                                <li><code>qt6-qtbase</code></li>
                            </list>
                        </tab>
                        </tabs>
                    </li>
                </list>
            </tab>
        </tabs>
    </tab>
</tabs>

<warning>
Please note that the application build is not currently available, 
so production dependencies may differ in the future.
</warning>