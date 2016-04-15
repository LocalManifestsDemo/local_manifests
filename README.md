Local Manifests
===============

Additional remotes and projects may be added through local manifest
files stored in `$TOP_DIR/.repo/local_manifests/*.xml`.

For example:

    $ ls .repo/local_manifests
    local_manifest.xml
    another_local_manifest.xml

    $ cat .repo/local_manifests/local_manifest.xml
    <?xml version="1.0" encoding="UTF-8"?>
    <manifest>
      <project path="manifest"
               name="tools/manifest" />
      <project path="platform-manifest"
               name="platform/manifest" />
    </manifest>

Users may add projects to the local manifest(s) prior to a `repo sync`
invocation, instructing repo to automatically download and manage
these extra projects.

Manifest files stored in `$TOP_DIR/.repo/local_manifests/*.xml` will
be loaded in alphabetical order.

Additional remotes and projects may also be added through a local
manifest, stored in `$TOP_DIR/.repo/local_manifest.xml`. This method
is deprecated in favor of using multiple manifest files as mentioned
above.

If `$TOP_DIR/.repo/local_manifest.xml` exists, it will be loaded before
any manifest files stored in `$TOP_DIR/.repo/local_manifests/*.xml`.


使用案例
===============

CyanogenMod(CM)适配了上百款机型，不同机型所涉及到的git库很可能是有差异的。以CM对清单文件的定制为例，通过新增local_manifest.xml，内容如下：

    <manifest>
        <!-- add github as a remote source -->
        <remote name="github" fetch="git://github.com" />

        <!-- remove aosp standard projects and replace with cyanogenmod versions -->
        <remove-project name="platform/bootable/recovery" />
        <remove-project name="platform/external/yaffs2" />
        <remove-project name="platform/external/zlib" />
        <project path="bootable/recovery" name="CyanogenMod/android_bootable_recovery" remote="github" revision="cm-10.1" />
        <project path="external/yaffs2" name="CyanogenMod/android_external_yaffs2" remote="github" revision="cm-10.1" />
        <project path="external/zlib" name="CyanogenMod/android_external_zlib" remote="github" revision="cm-10.1" /> 

        <!-- add busybox from the cyanogenmod repository -->
        <project path="external/busybox" name="CyanogenMod/android_external_busybox" remote="github" revision="cm-10.1" />

    </manifest>

local_manifest.xml会与已有的default.xml融合成一个项目清单文件manifest.xml，实现了对一些git库的替换和新增。
可以通过以下命令导出当前的清单文件，最终snapshot.xml就是融合后的版本：

	$ repo manifest -o snapshot.xml -r

在编译之前，保存整个项目的清单，有助于问题的回溯。当项目的git库发生变更，需要回退到上一个版本进行验证的时候，只需要重新基于snapshot.xml初始化上一个版本即可：

    $ cp snapshot.xml .repo/manifests/
    $ repo init -m snapshot.xml           # -m 参数表示自定义manifest
    $ repo sync -d                        # -d 参数表示从当前分支脱离，切换到manifest中定义的分支

