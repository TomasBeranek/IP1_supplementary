General information
The following directories contain the CAMEA source code test results. Testing was performed by the
tool for static analysis - Facebook Infer (https://fbinfer.com/). Infer itself can only analyze 
source code written in Java/C/C++/Objective-C language, therefore only the following parts of the 
CAMEA code have been analyzed by Infer:
	-MMRT
	-drivers
	-Detector_C


Directory structure 
Each part of the CAMEA code has its own folder, which contains the results of an analysis of three
versions of Infer. Each result folder contains:

	Detector_C 		      # part of CAMEA code		
	│
	└── infer		      # version of Infer (in this case Infer v0.16.0)
		│
    		├── infer-out/	      # the output of the Infer version itself for bug trace
    		│
    		├── issues_ALL.txt    # summary of issues in ALL source code used by that part
    		│     		      # (including libraries, system header files, etc.)
    		│
    		└── issues_CAMEA.txt  # summary of issues that only occur in the CAMEA source code


Versions of Infer
Three Infer versions were used for the analysis:
	-official version of Infer v0.16.0 
	(https://fbinfer.com/)
	-Infer with Atomicity Violations Analyser plugin 
	(https://github.com/harmim/infer/wiki/Atomer:-Atomicity-Violations-Analyser)
	-Infer with Low Level Deadlock Detector plugin 
	(https://pajda.fit.vutbr.cz/xmarci10/fbinfer_concurrency/wikis/L2D2:-A-Low-Level-Deadlock-Detector)


Quick issues summary
  Detector_C
  ├── infer
  │   ├── full code		-64 issues 
  │   └── only CAMEA code	-no issues
  ├── infer_atomicity
  │   ├── full code		-no issues
  │   └── only CAMEA code	-no issues
  └── infer_deadlock
      ├── full code		-no issues
      └── only CAMEA code	-no issues
  drivers
  ├── infer
  │   ├── full code		-614 issues
  │   └── only CAMEA code	-2 issues
  ├── infer_atomicity
  │   ├── full code		-no issues
  │   └── only CAMEA code	-no issues
  └── infer_deadlock
      ├── full code		-no issues
      └── only CAMEA code	-no issues
  MMRT
  ├── infer
  │   ├── full code		-105 issues
  │   └── only CAMEA code	-9 issues
  ├── infer_atomicity
  │   ├── full code		-no issues
  │   └── only CAMEA code	-no issues
  └── infer_deadlock
      ├── full code		-no issues
      └── only CAMEA code	-no issues


How to run a bug trace
To run a bug trace, you will need the Infer version that analyzed the source code, as the Infer
versions may not be compatible with each other. Enter the directory that contains the infer-out/ 
directory and run the following command:

	infer explore

and enter the issue number (in case of issue in CAMEA code, it is listed in issues_CAMEA.txt)


How to rerun analysis
To run an analysis again, you will need the Infer version that analyzed the source code, as the Infer
versions may not be compatible with each other. It is recommended to back up the old output folder
as the new analysis (with default output settings) will overwrite the old output. Enter the directory
that contains the infer-out/ directory and run the following command:

	infer analyze [options]


Changes in source code
Since Infer uses its own internal compiler, there may be cases where Infer does not syntactically 
accept the source code. Therefore, it was necessary to make changes, so that the source code could 
be analyzed. Changes that could result in an issue should be captured by Infer, and it should be 
possible to use bug trace to determine if the issue is caused by the change. The following
changes were made:

In part: MMRT
In file: src/detector/classifier_loader/DetRectangle.h (line 90)
Changed from:

	DetRectangle<decltype(std::declval<DetRectangle<T>>().w + std::declval<DetRectangle<Tr>>().w)> operator +(const DetRectangle<Tr> &r)

to:

	DetRectangle<unsigned char> operator +(const DetRectangle<Tr> &r)

and the following three operators have been changed similarly

In part: drivers
In file: dma_axi.c (line 7)
	This header file uses macros containing the asm_goto construction. These macros have been
	replaced by empty macros in file compiler_types.h (line 188)



Detailed information about each version
Infer v0.16.0
	Active checkers:
		annotation reachability (C/C++/ObjC, Java), 
		biabduction (C/C++/ObjC, Java), 
		buffer overrun analysis (C/C++/ObjC, Java), 
		buffer overrun checker (C/C++/ObjC, Java), 
		liveness (C/C++/ObjC), 
		ownership (C/C++/ObjC), 
		pulse (C/C++/ObjC), 
		quandary (C/C++/ObjC, Java), 
		RacerD (C/C++/ObjC, Java), 
		SIOF (C/C++/ObjC), 
		uninitialized variables (C/C++/ObjC), 
		Starvation analysis (C/C++/ObjC, Java)
	Infer run command:
		infer run --annotation-reachability --bufferoverrun --cxx --cxx-infer-headers --headers --liveness --ownership --no-cost --print-active-checkers --pulse --quandary --source-files --starvation -- <build_command>

Infer with Low Level Deadlock Detector Plugin
	Active checkers:
		Deadlock analysis (C/C++/ObjC)
	Infer run command:
		infer run --cxx --cxx-infer-headers --headers --print-active-checkers --deadlock-only -- <build_command>

Infer with Atomicity Violations Analyser Plugin
	Active checkers:
		Atomicity violations analysis - detection of atomicity violations (C/C++/ObjC)
	Infer run command:
		infer-atomic run --cxx --cxx-infer-headers --headers --print-active-checkers --atomic-sequences-only -- <build_command>
		<build_tool> clean  # if building tool doesnt compile unchanged files
		infer-atomic run --cxx --cxx-infer-headers --headers --print-active-checkers --atomicity-violations-only -- <build_command>


Contact
In case of any questions, do not hesitate to contact authors:
	Tomas Beranek (xberan46@stud.fit.vutbr.cz)
	Prof. Ing. Tomas Vojnar Ph.D. (vojnar@fit.vutbr.cz)
