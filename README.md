## Timing-Driven Variation-Aware Clock Mesh Synthesis Environment ##
## Ameer M.S. Abdelhadi; ameer.abdelhadi@gmail.com ##
- - -
***LICENSE:*** BSD 3-Clause ("BSD New" or "BSD Simplified"); 2010
- - -
<BR>
Please refer to the full paper for more information:
<P>
	A. Abdelhadi, R. Ginosar, A. Kolodny, and E. G.  Friedman,
	<BR>"Timing-Driven Variation-Aware Nonuniform Clock Mesh Synthesis,"
	<BR><EM>Proceedings of the ACM/IEEE Great Lakes Symposium on VLSI (GLSVLSI '10)</EM>, pp. 15-20, May 2010.
	[Paper: <A HREF='publications/Abdelhadi-Conference-2010May-GLSVLSI2010-ClockMeshSynthesis.pdf'>PDF</A>,
	        <A HREF='http://dx.doi.org/10.1145/1785481.1785487'                                   >DOI</A>]
	[Talk:  <A HREF='publications/Abdelhadi-Talk-2010May-GLSVLSI2010-ClockMeshSynthesis.pdf'      >PDF</A>,
	        <A HREF='publications/Abdelhadi-Talk-2010May-GLSVLSI2010-ClockMeshSynthesis.pptx'     >PPT</A>]
</P>

<BR>
<BR>

## Files and directories in this package: ##
```
scr/                      : Scripts directory, including Perl and TCL scripts
scr/pm/                   : Perl modules (packages)
scr/share/man             : Manuals for the Perl modules
scr/syn/                  : Logic and physical synthesis script 
scr/syn/compile_bgx.tcl   : Compile Script for Cadence BuildGates
scr/syn/compile_dc.tcl    : Compile Script for Synopsys DesignCompiler
scr/syn/encounter.conf    : Configuration file for Cadence First Encounter
scr/syn/encounter.mesh.tcl: Compile Script for Cadence Encounter with clock mesh synthesis
scr/syn/encounter.tcl     : Compile Script for Cadence Encounter
scr/syn/net2grp.pl        : Converts Verilog netlist into graph; Generate registers connectivity graph 
scr/syn/sta2xml.tcl       : Generates a timing constrains graph from netlist (integrates with Cadence PrimeTime flow)
scr/grid/                 : Grid (mesh) generation scripts
scr/grid/png/             : Outputs as PNG images
scr/grid/xml/             : input XML files describing circuits' STA
scr/grid/lup/             : input LUP files including grid weights
scr/grid/aux.pm           : auxiliary functions module
scr/grid/grid.pl          : Clock grid (mesh) generator 
scr/grid/lef.pm           : LEF file parser module
scr/grid/misc.pm          : Miscellaneous functions module
scr/grid/route.pm         : Mesh routing module
syn/                      : Synthesis directory
syn/rtl/                  : ISCAS'95 sequential Verilog benchmarks
syn/log/                  : log files
syn/out/                  : Output files, reports and results
syn/encounter.tcl         : Compile Script for Cadence Encounter
syn/encounter.cmd         : Encounter Command Logging File
```

## Running Synthesis: ##

```
cd syn
setenv TOPLVL s838_1
setenv OSULIB /hp/ameer/cgs/lib
setenv SYNDIR $PWD
setenv SYNSCR /hp/ameer/cgs/scr/syn
setenv FRQMHZ 1000
```

If separated STDOUT and STDERR are required, use:
```
(bgx_shell -f $SYNSCR/compile_bgx.tcl > out/${TOPLVL}.bgx.log) >& out/${TOPLVL}.bgx.err
(encounter -config $SYNSCR/encounter.tcl -nowin > out/${TOPLVL}.enc.log) >& out/${TOPLVL}.enc.err
```

Otherwise, use:
```
bgx_shell -f $SYNSCR/compile_bgx.tcl|tee out/${TOPLVL}.bgx.log
encounter -config $SYNSCR/encounter.tcl -overwrite -nowin | tee out/${TOPLVL}.enc.log
```

## Important scripts: ##

<BR>
***scr/grid/grid.pl***
```
SYNOPSIS:
  Generate clock grid
USAGE:
  grid.pl -xml <xml>
PARAMETERS:
  -tcgxml|tcg|xml  <xml>: input verilog netlist file
  -lookuptable|lup <lup>: skew/grid density lookup table
  -weights|w            : grid weights
  -drawin |di      <png>: draw input netlist graph into <png file> 
  -drawout|do      <png>: draw output netlist graph into <png file> 
  -drawgraph|dg    <png>:
  -help|h: print this massege 
  > (-tcgxml) and at least one of (-lup) or (-weights) are required
EXAMPLE:
  grid.pl -xml s27.xml -di -do -dg -w "0.5 0.51 0.52 0.53 0.54 0.55"
  grid.pl -xml s208_1.xml -di -do -dg -w "0.2 0.25 0.3 0.35 0.4"
  grid.pl -xml s349.xml -di -do -dg -w "0.1 0.15 0.2 0.25"
  grid.pl -xml s382.xml -di -do -dg -w "0.1 0.15 0.2 0.25 0.3 0.35 0.4 0.45 0.5"
  grid.pl -xml s838_1.xml -di -do -dg -w "0 0.01 0.02 0.03 0.03 0.05 0.06 0.07 0.08 0.09 0.1 1.1"
  grid.pl -xml s1423.xml -di -do -w "\-0.7 \-0.6 \-0.5 \-0.4 \-0.3 \-0.2 \-0.1 0 0.1 0.2 0.3 0.4 0.5"
```

<BR>
***scr/syn/net2grp.pl***
```
SYNOPSIS:
  Converts Verilog netlist into graph
  Generate registers connectivity graph
USAGE:
  net2grp.pl -net <verilog netlist> [-rc [<tcl>] -di [<png>] -do [<png>]]
PARAMETERS:
  -netlist|net <net file>: input Verilog netlist file
  -regscon|rc  <tcl file>: make all cells but registers transparent
                           save only registers connectivity
                           report to <tcl file>, if isn't specified report to STDOUT
  -drawin |di  <png file>: draw input netlist graph into <png file> 
                           if <png file> isn't specified draw to <net file>.in.png
  -drawout|do  <png file>: draw output netlist graph into <png file> 
                           if <png file> isn't specified draw to <net file>.out.png
  -help|h: print this message 
  > (-netlist) and at least one of (-regscon) or (-drawin) or (-drawout)are required
EXAMPLE:
  congrp.pl -net s27.bgx.vh -rc
```

<BR>
***scr/syn/sta2xml.tcl***
```
SYNOPSIS:
  Generates a timing constrains graph from netlist;
  Integrates with Cadence PrimeTime flow
MAIN FUNCTION:
  reg2xml
ARGUMENTS:
  - regs     : participant registers
  - xmlfn    : output xml file name
  - netlistfn: netlistfn
EXAMPLE:
  reg2xml [all_registers] \
          [file join $my_syndir $my_outdir "$my_toplvl.enc.xml"] \
          [file join $my_syndir $my_outdir "$my_toplvl.bgx.vh" ]
```
