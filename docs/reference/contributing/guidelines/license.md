<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
### License

This chapter covers the different aspects of developing your own libraries for use in Arm Mbed devices, as well as items to keep in mind during development, such as licensing. It covers:
- [Creating and licensing](#licensing-binaries-and-libraries): Create and license your own binaries and libraries.
- [The Arm Mbed OS codebase](#contributing-to-the-mbed-os-code-base): Use GitHub to contribute additions and bug fixes to Mbed OS itself.

#### Licensing binaries and libraries

When you write original code, you own the copyright and can choose to make it available to others under a license of your choice. A license gives rights and puts limitations on the reuse of your code by others. Not having a license means others cannot use your code. We encourage you to choose a license that makes possible (and encourages!) reuse by others.

If you create new software, such as drivers, libraries and examples, you can apply whatever license you like as the author and copyright holder of that code. Having said that, we encourage you to use a well-known license such as one of the ones listed <a href="http://spdx.org/licenses/" target="_blank">on the SPDX License List</a>, preferably an <a href="https://opensource.org/licenses/alphabetical" target="_blank">OSI-approved</a>, permissive open source software license. Specifically, we recommend the following:

- For original source code, use the Apache 2.0 license.  

- For binary releases (for example, private source code you can’t or don’t want to release but want to share as a binary library and headers available for others to use), consider the <a href="https://www.mbed.com/licenses/PBL-1.0" target="_blank">Permissive Binary License</a>. This is designed to be compatible with Apache 2.0 and the Mbed OS codebase.

- If your software incorporates or is derived from other third party open source code, please be sure to retain all notices and identify the license for the third party licensed code in the same manner as described below. Remember, you cannot change the license on someone else’s code, because you are not the copyright holder! Instead, choose a license that is compatible with the license used by the third party open source code, or use the same license as that code. For example, if your software is derived from GPL source code, GPL requires you to license the rest of your code in that software under the GPL too. Note that many commercial users won’t be able to use GPL source code in their products, so we don't recommend this license if you're not obligated to use it.

You must either write all the code you provide yourself, or have the necessary rights to provide code written by someone else.

In all cases, whatever license you use, please use an <a href="http://spdx.org/licenses/" target="_blank">SPDX license identifier</a> in every source file following the <a href="https://spdx.org/spdx-specification-21-web-version#h.twlc0ztnng3b" target="_blank">recommendations</a> to make it easier for users to understand and review licenses.

#### When to use Apache 2.0

Apache 2.0 is a permissive, free and open source software license that allows other parties to use, modify and redistribute the code in source and binary form. Compared to the often used BSD license, Apache 2.0 provides an express patent grant from contributors to users.

The full text of the license can be found on the [Apache website](http://www.apache.org/licenses/LICENSE-2.0). For more information about Apache 2.0, see [the FAQ](http://www.apache.org/foundation/license-faq.html).

#### How to apply Apache 2.0 correctly

In order to clearly reflect the Apache 2.0 license, please create two text files:

- A *LICENSE* file with the following text:</br>

<pre>Unless specifically indicated otherwise in a file, files are licensed under the Apache 2.0 license,
as can be found in: LICENSE-apache-2.0.txt</pre>

- The full original <a href="http://www.apache.org/licenses/LICENSE-2.0" target="_blank">Apache 2.0 license text</a> in *LICENSE-apache-2.0.txt*

Each source header should *start with* your copyright line, the SPDX identifier and the Apache 2.0 header as shown here:

```
Copyright (c) [First year]-[Last year], **Your Name or Company Here**
SPDX-License-Identifier: Apache-2.0

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.

You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied.

See the License for the specific language governing permissions and limitations under the License.
```

#### When to use the Permissive Binary License

The Permissive Binary License (PBL) is a permissive license based on BSD-3-Clause and designed specifically for binary blobs. It's minimal but covers the basics, including an express patent grant.

It allows you to share a binary blob and the relevant headers, and allows others to use that binary blob as part of their product - as long as they provide it with all the relevant dependencies and don't modify it or reverse engineer it.

The full text can be found on <a href="https://www.mbed.com/licenses/PBL-1.0" target="_blank">mbed.com</a>.

#### How to apply PBL correctly

In order to clearly reflect the PBL license, please create three text files:

- A *LICENSE* file with:

<pre>Unless specifically indicated otherwise in a file, files are licensed under the Permissive Binary License,
as can be found in: LICENSE-permissive-binary-license-1.0.txt</pre>

- The full original <a href="https://www.mbed.com/licenses/PBL-1.0" target="_blank">Permissive Binary License 1.0 text</a> in *LICENSE-permissive-binary-license-1.0.txt*.

- A *DEPENDENCIES* file with the dependencies that this binary requires to work properly. This is to make sure that third parties integrating the binary in their own distribution are aware that they need to include the relevant dependencies. If your binary does not have any dependencies, the file should state so (that is, say “No dependencies”); don't omit this file.

Each source header should *start with* your copyright line, the SPDX identifier and the PBL header:

```
Copyright (c) [First year]-[Last year], **Your Name Here**, All Rights Reserved
SPDX-License-Identifier: LicenseRef-PBL

This file and the related binary are licensed under the Permissive Binary License, Version 1.0 (the "License"); you may not use these files except in compliance with the License.

You may obtain a copy of the License here: LICENSE-permissive-binary-license-1.0.txt and at
https://www.mbed.com/licenses/PBL-1.0

See the License for the specific language governing permissions and limitations under the License.
```

#### Using a different license

If you decide to use a different license for your work, follow the same pattern:

- Create a *LICENSE* file with a description of the license situation, following the pattern described in the sections above.

- Put the full original license texts in separate documents named *LICENSE-XYZ.txt*, where XYZ is the corresponding <a href="http://spdx.org/licenses/" target="_blank">SPDX identifier</a> for your license.

- Begin each source header with your copyright line, the SPDX identifier and the standard header for the license that applies to that single file, if it has one. (See <a href="https://spdx.org/spdx-specification-21-web-version#h.twlc0ztnng3b" target="_blank">SPDX Specification, Appendix V</a>.)

- If more than one license applies to the source file, then use an SPDX license expression (see <a href="https://spdx.org/spdx-specification-21-web-version#h.jxpfx0ykyb60" target="_blank">SPDX Specification, Appendix IV</a>), to reflect the presence of multiple licenses in your *LICENSE* file and in each source file.

#### Contributing to the Mbed OS codebase

##### Mbed OS principles

Mbed OS uses these same basic principles for its source code and library distributions. So source code we own is distributed under the Apache 2.0 license, and binary blobs are released under the Permissive Binary License. Software parts from third parties that were already licensed under a different license are available under that original license.

All the source code and binary blobs that end up in Mbed OS are maintained in public GitHub repositories.
