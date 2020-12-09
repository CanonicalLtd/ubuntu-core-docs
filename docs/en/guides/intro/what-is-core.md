```{=html}
<!-- 
Version: 2.0
-->
```
Ubuntu Core is Ubuntu for IoT and embedded environments, optimised for security and reliable updates. It's easy to deploy, tamper-resistant, and hardened against corruption.

Its read-only root filesystem is built from the same packages used to build the wider set of Ubuntu distributions, and only differs in the way packages are delivered, and crucially, updated. This is all handled by snaps, a secure, confined, dependency-free, cross-platform Linux packaging system.

Snap packages ensure there is always a clean separation between the base system and whatever applications need to be installed, as well as isolation between each and every application, their data, and even application version data.

Updates are transactional, which means they’re either 100% successful or they’re not installed. If they're not installed, they leave no trace of any failure other than log details. This means the system remains fully operational and in a continuously well-defined state during both application and system updates. The system can also recover or revert to previous states if necessary, even if a system fails to boot. Unlike alternative or more traditional package managers, a failed update never leaves the system in an unpredictable state.

<h3 id="heading--advantages-for-iot">Advantages for IoT</h3>

Ubuntu Core is engineered to meet the precise requirements of IoT devices. It runs on a large range of hardware, including Raspberry Pi, Intel NUC, Qualcomm Snapdragon 410c and even a KVM. Features include:

-   **simple, consistent installation and deployment**: Ubuntu Core is installed via an immutable image, which can be either installed or built specifically for your platform and application
-   **a read-only filesystem**: apps run in isolation from each other and access to system resources is only granted with explicit permissions
-   **transactional updates**: signed, autonomous and atomic, updates can withstand unpredictable hardware and network conditions, even to the operating system
-   **snap-based, security first**: snaps are secure, easy to build, and painless to distribute. Public/private key validation ensures what's running is exactly what's intended to run

Designed from the ground-up to solve the complex problems associated with deploying, running and updating critical software, **Ubuntu Core** makes updates automatic, reliable, secure and transparent whilst still offering a broad and carefully selected level of control and customisation over an update’s deployment.

<h4 id="heading--ubuntu-core-and-ubuntu">Ubuntu Core and Ubuntu</h4>

The Ubuntu family of Linux distributions includes Ubuntu Desktop and Ubuntu Server, as well as Ubuntu Cloud which is optimised and certified for most major clouds. Ubuntu Core is built from the same foundations but differs in the way those foundations are packaged and deployed.

| **Comparison**       | Ubuntu Core                                                                         | Ubuntu                                                             |
|----------------------|-------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| Minimum requirements | 500Mhz single-core CPU `<br />`{=html} 384MB RAM, 512MB storage                     | 1GHz dual-core CPU `<br />`{=html} 512MB RAM, 1.5GB storage        |
| Packaging            | Snaps, with autonomous updates via either the global store or a private brand store | Debs and snaps, with traditional update mechanics and organisation |
| App security         | Confinement via AppArmor / Seccomp                                                  | No system-wide confinement for deb packages                        |
| Graphical UI         | None by default (Wayland or Mir are options)                                        | X.Org/GNOME or Wayland/GNOME                                       |
