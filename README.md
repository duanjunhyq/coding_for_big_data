# Coding for big-data analysis
X things you should take into consideration if you code for big data analysis


This idea came up during a conversation with [Marc Vaudel](https://github.com/mvaudel) and [Ira Cooke](https://github.com/iracooke). During the development of GalaxyP we discovered a lot of tools that were following bad habits, that makes it hard to integrate these tools into a workflow management system like Galaxy, or more generally into HPC environments.

In general if you target cluster environments or big data analysis, you want to run your program in parallel by multiple users at the same time. This means you need to abstract handling of configurations and file management (temporary and persistent) from a single user.

Here we want to collect some ideas that you should take into consideration if you target big data computing and multiple users in parallel.

1. Start with testing the software from the very beginning of your setup and work towards the end - this includes testing of the installer as well as functional and unit tests. There are many tools that help testing for virtually every programming language. Testing may also include syntax checking, coveralls testing and automatic memory leak checking.

2. Always work from a future perspective. Choose function, object names and arguments very very carefully such that they will never need to be changed in the future, to avoid backwards incompatibility at any time.

3. Don’t assume you have a home directory. (.openms, .matplotlib). On cluster environments jobs/taks usually don’t have a home directory.

4. Use a tmp dir to store intermediate files. To figure out where to store temporary files, use predefined libraries from your favorite programming language (like tempfile in python). This enables the cluster admin to insert fast local hard-drives for temporary storage that will speed up your calculation.
  * Ideally, also  provide an option to use a custom tmp directory

5. Give the user (in this case it is the cluster user, or the workflow management system) injection points to control data flow … e.g. by using $ENV vars to override parameters (e.g. PEPTIDE_SHAKER_TEMP_DIR).

6. Do not rely on file extensions to determine the filetype. As default setting this is good, but offer at least an alternative way to tell your program which file type is provided. File extensions are not standardised. Moreover, data management system might store the filename and other metadata in a database separated from the filesystem blob on your harddrive, without file extension, without filename. Usually these files are identified by a hashsum or a UUID. Similarly, if you define a novel file format, take into consideration that a system is able to identify it as such by making use of the POSIX magic system.

7. Do not require input files to have one particular filename or pattern.

8. Ideally, allow the ability to customize completely the filename of every output file generated by the tool
  * If you for any reason can not reasonably take the exact file name for every output file (like if the number of outputs is unknown, or very large) but need to take a file name pattern - do allow to specify output folder in addition to the file name pattern

9. Try to avoid using metadata files (file.ext + file.ext.ext2) but include metadata within a metadata or header section of a file format.
  * Include enough information to unambiguously identify the filetype, version, and meaning behind values (e.g. column names).
  * Also include information about the generating program, including version and complete set of parameters.

10. Don’t write to stderr unless there is an error. stderr is only for error, don’t write warnings or any results to this output stream. For reporting errors, make use of functions that give a stack trace, to track the cause back more quickly.

11. Make your tool streaming aware. Filesystem IO is becoming more and more the bottleneck in modern computing. Try to stream your outputs as much as possible, so it can be consumed by other programs easily without touching the hard drive.

12. Stick to standards: standard file types, standard ontologies (EDAM), standard command line arguments and naming schemes. (--help, --version, -h, -v) and do not create a dialect or derivate file format.

13. Include a --version command to enable reproducibility by capturing the version number of your tool easily. Try to avoid to have the version number at multiple locations within your code; try to set your system up in such a way that the version definition is included. If possible, try to include the version control systems revision number into the output of the version (sometool v1.2.3-5483e9f5bf4d725e3).

14. Do not reuse or repurpose existing or previously used short or long argument names.

15. Do not reinvent the wheel, stick to your roots. E.g. if your tool consumes a multiple FASTA file and operates on each unit independently, it’s a good idea to split the FASTA file into small chunks and speed up your tool by using multiple processors. But this idea is handled by larger frameworks, don’t implement this by yourself. Similarly, make use of available libraries (e.g. biopython, pysam, seqan, biojava, biojs) and include the version of the used dependency in the installation script, but beware of licensing conflicts.

16. Try to make use of the default installation mechanism for that particular language. There are many tools that do not obey these rules and required patching and additional scripting in order to get installed.

17. Do not include compiled binaries nor external source code within your version control system. Binaries should be either generated from the code base or should be provided by a package management system. External source code should be resolved with the installation mechanism (e.g. GNU Autotools, pip, Pom/Maven, etc.).

18. Use the force Luke. The shebang is a powerful one line of code, but you should use it in a correct way. Use `/usr/bin/env python` or `/usr/bin/env perl` instead of `/usr/bin/perl`. It is not guaranteed that perl is installed in `/usr/bin/`. Especially not if you use concrete versions of a software in virtual environments, like conda is producing them.

19. Provide an accurate, but concise changelog between versions. This changelog should be easily understandable by the typical end user, and clearly note changes of importance, including changes in behavior and default settings, deprecated and new parameters, etc. It should not include changes that, while important, do not impact a typical end user, such as internal architectural changes. In other words, this is not simply the unannotated output of `git log`.


Authors
-------
