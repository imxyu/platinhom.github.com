---
layout: post
title: antechamber使用
date: 2015-09-16 12:56:55
categories: CompCB
tags: CompBiol MD
---

Usage: antechamber options

-i -o -fi and -fo must be appear in command lines and the others are optional

- **-i**   input file name
- **-fi**  input file format
- **-o**  output file name
- **-fo**  output file format
- **-c**  charge method
- -cf  charge filename
- -nc  net molecular charge (int)
- -a   additionalt file name
- -fa  additionalt file format
- -ao  additionalt file operation
    - crd : only read in coordinate
    - crg: only read in charge
    - radius: only read in radius *(new version)*
    - name  : only read in atom name
    - type  : only read in atom type
    - bond  : only read in bond type 
- -m   mulitiplicity (2S+1), default is 1
- -rn  residue name, if not available in the input file, default is MOL
- -rf  residue toplogy file name in prep input file, default is molecule.res
- -mp  mopac program name, default is mopac.sh *(old version)*
- -mk  mopac keyword in a pair of quotation mark *(old version)*
- -ek  mopac or sqm keyword, inside a pair of quotes *(new version)*
- -gk  gaussian keyword in a pair of quotation mark
- -ch  check file name for gaussian, default is 'molecule' *(new version)*
- -gm  gaussian memory keyword, inside a pair of quotes, such as"%mem=1000MB" *(new version)*
- -gn  gaussian number of processors keyword, inside a pair of quotes, such as "%nproc=8" *(new version)*
- -gv  add keyword to generate gesp file (for Gaussian 09 only) *(new version)*
	- 1    : yes
	- 0    : no, the default
- -ge  gaussian esp file generated by iop(6/50=1), default is g09.gesp *(new version)*
- -df  am1-bcc precharge flag, 2 - use sqm(default); 0 - use mopac *(new version)*
- -at  atom type, can be gaff, amber(for PARM94/99/99SB), bcc and sybyl, default is gaff
- -du  fix atom name duplications, can be yes(y) or no(n), default is yes *( default no in old version)*
- -bk  4-character component Id, for ccif *(new version)*
- -an  adjust atom names: yes(y) or no(n), the default is 'y' for 'mol2' and 'ac' and 'n' for the other format *(new version)*
- -j   atom type and bond type prediction index, default is 4 
    - 0    : no assignment
    - 1    : atom type 
    - 2    : full  bond types 
    - 3    : part  bond types 
    - 4    : atom and full bond type 
    - 5    : atom and part bond type 
- -s   status information can be 0 (brief), 1 (the default) and 2 (verbose)
- -eq  equalizing atomic charge, default is 1 for '-c resp' and '-c bcc' and 0 for the other charge methods *(new version)*
	- 0    : no use
	- 1    : by atomic paths 
	- 2    : by atomic paths and structural information, i.e. E/Z configurations
- **-pf**  remove the intermediate files: can be yes (y) and no (n), default is no
- -pl  maximum path length to determin equivalence of atomic charges for resp and bcc, the smaller the value, the faster the algorithm, default is -1 (use full length), set this parameter to 10 to 30 if your molecule is big (# atoms >= 100) *(new version)*
- -L	list the supported file formats and charge methods

#### List of the File Formats  

file format type  | abbre.  | index | file format type  | abbre.   |index
Antechamber       |  ac     |    1  | Sybyl Mol2        |   mol2   |   2 
PDB               |  pdb    |    3  | Modifiled PDB     |   mpdb   |   4 
AMBER PREP (int)  |  prepi  |    5  | AMBER PREP (car)  |   prepc  |   6 
Gaussian Z-Matrix |  gzmat  |    7  | Gaussian Cartesia |   gcrt   |   8 
Mopac Internal    |  mopint |    9  | Mopac Cartesian   |   mopcrt |  10 
Gaussian Output   |  gout   |   11  | Mopac Output      |   mopout |  12 
Alchemy           |  alc    |   13  | CSD               |   csd    |  14 
MDL               |  mdl    |   15  | Hyper             |   hin    |  16 
AMBER Restart     |  rst    |   17  | Jaguar Cartesian  |   jcrt   |  18 
Jaguar Z-Matrix   | jzmat   |   19  | Jaguar Output     |   jout   |  20
Divcon Input      | divcrt  |   21  | Divcon Output     |   divout |  22
SQM Input         | sqmcrt  |   23  | SQM Output        |   sqmout |  24
Charmm            | charmm  |   25  | Gaussian ESP      |   gesp   |  26
Component cif     | ccif    |   27  |                   |          |   

AMBER restart file can only be read in as additional file. Old version may just support type 1-17.

--------------------------------------------------------------

#### List of the Charge Methods  

charge method    | abbre. | index | charge method   | abbre. | index  
RESP             |  resp  |   1   |  AM1-BCC        |   bcc  |    2
CM1              |  cm1   |   3   |  CM2            |   cm2  |    4
ESP (Kollman)    |  esp   |   5   |  Mulliken       |   mul  |    6
Gasteiger        |  gas   |   7   |  Read in charge |   rc   |    8
Write out charge |  wc    |   9   |  Delete Charge  |   dc   |   10

Old version may just not support type 3 and 10.

- 在老版Amber中是使用`Mopac`算电荷的, 后来的版本就加入了`sqm`程序来算电荷. 在老版本中支持cm1,cm2等电荷, 但新版本中SQM和高斯都不能算CM1和CM2.  
- 高斯结果只能转出成resp(用gesp)和esp(用gout), 不能转成Mulliken,虽然out里面有Mulliken Charge, 但和BCC算出的Mulliken相差甚远..另外gesp不能产生ESP电荷.  
- 用antechamber算Gasteiger, Mulliken和AM1-BCC都是可以的, 后两者需要调用sqm. 即使用gout也不能产生出BCC和Mulliken.   
- 转为pdb是没有电荷的.mol2/ac/mpdb/都可以带电荷.

----------------------------------------------------------------

## 例子:
用ligand.mol2作例子:

~~~bash
# Calculate bcc charge for input mol2
antechamber -i ligand.mol2 -fi mol2 -o ligand_bcc.mol2 -fo mol2 -c bcc -pf y
# Convert file to prepi and use parmchk to generate amber input parameter
antechamber -i ligand_bcc.mol2 -fi mol2 -o ligand_bcc.prep -fo prepi -pf y
parmchk -i ligand.prep -f prepi -o ligand.frcmod
# in tleap: loadAmberPrep ligand.frcmod

# Directly generate prepi file from mol2 with bcc charge, not commend
antechamber -i ligand.mol2 -fi mol2 -o small_bcc.prep -fo prepi -c bcc -pf y

# Convert mol2 <-> Gaussian input/output for charge calculation for esp/resp
antechamber -i ligand.mol2 -fi mol2 -o ligand.gjf -fo gcrt -pf y -gn "%nproc=8" -gm "%mem=1000MB"
antechamber -i ligand.log -fi gout -o ligand_resp.mol2 -fo mol2 -c resp -pf y

# Write out the charge to charge file
antechamber -i ligand_bcc.mol2 -fi mol2 -c wc -cf ligand_c.crg
# Load known charge to molecule. The charge file ligand_c.crg is f10.8  8charge/per line
antechamber -i ligand.mol2 -fi mol2 -o ligand_c.mol2 -fo mol2 -c rc -cf ligand_c.crg
~~~


### Update 

- 在使用antechamber转格式时, mol2输出时默认gaff 原子类型, 要保持原有类型, 使用`-at sybyl`或者`-j 0/2/3`
- 转mol2键级时, 羧基antechamber会解释为1/2键级, 而一般mol2是ar.


## 子程序

### atomtype

- -i input file name
- -o output file name(ac)
- -f input file format(ac (the default) or mol2)
- -p amber or gaff or bcc or gas or sybyl, it is supressed by "-d" option
- -d atom type defination file, optional
- -a do post atom type adjustment (it is or sybyl applied with "-d" option) 1: yes, 0: no (the default)

类型定义文件在`$AMBERHOME/dat/antechamber`, ATOMTYPE\_AMBER.DEF (amber), ATOMTYPE\_GFF.DEF (general amber force field), ATOMTYPE\_BCC.DEF (AM1-BCC), ATOMTYPE\_SYBYL.DEF (sybyl) and ATOMTYPE\_GAS.DEF (gasteiger). 使用-p来选择.

原子类型输出在最后一列.

~~~
ATOM      1  C   <1>     1       1.208   0.697   0.000  0.000000      C.ar
ATOM      2  C   <1>     1       2.494   1.441   0.000  0.000000       C.3
ATOM      3  H1  <1>     1       2.295   2.512  -0.000  0.000000         H
ATOM      4  O   <1>     1       3.238   1.099  -1.158  0.000000       O.3
~~~

### bondtype

- -i input file name 
- -o output file name 
- -f file format (ac or mol2)
- -j judge bond type level option, default is part
	1. full  full judgement
	2. part  partial judgement, only do reassignment according to known bond type information in the input file
- -s stop running if APS (atomic penality score) is not available
	1. 0 - no, all the related bonds are frozen, the default
	2. 1 - yes

输出的bond types (single (1), double (2), triple (3), aromatic single (7), aromatic double (8), aromatic(10), delocalized (9) and conjugated (6))

参数文件在 `APS.DAT` in `$AMBERHOME/dat/antechamber`

输出文件中,储存在BOND部分.四个数字: 键序号, 两个原子序号, 键类型, 原子名(原子部分对应,非类型).

~~~
BOND    1    1    2    1      C    C
BOND    2    1    7    8      C    C
BOND    3    1   16    7      C    C
BOND    4    2    3    1      C   H1
~~~

### am1bcc

- -i input file name in ac format 
- -o output file name 
- -f output file format(pdb or ac, optional, default is ac)
- -p bcc parm file name (optional))
- -s status information, can be 0 (brief), 1 (the default) and 2 (verbose)
- -j atom and bond type judge option, default is 0)
	1. 0: No judgement
	1. 1: Atom type
	1. 2: Full bond type
	1. 3: Partial bond type
	1. 4: Atom and full bond type
	1. 5: Atom and partial bond type


## Reference

1. [antechamber](http://ambermd.org/antechamber/ac.html#antechamber)

------
