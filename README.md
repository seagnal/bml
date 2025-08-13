# BML - Binary Markup Language

**BML (Binary Markup Language)** is a lightweight, binary alternative to XML, designed for efficient data encoding and decoding in files or digital communication (sockets, digital buses, etc.). BML is optimized for performance and flexibility, allowing users to define their own tags and handle hierarchical data structures, similar to XML, but in a binary format.

## Features

- **Binary Format**: Unlike XML, BML is binary, making it more compact and efficient for both storage and transmission.
- **No Illegal Characters**: BML does not have any illegal characters, unlike XML (`&`, `<`, `>`).
- **Extensions**: Supports custom extensions for headers and content.
- **No Attributes**: Simplifies parsing and encoding by omitting attributes.
- **Lightweight**: Designed for fast encoding and decoding.
- **Flexible Tagging**: Tags can be strings, integers, or custom binary data.
- **Child Elements**: Supports nested child elements, just like XML.

## Key Differences from XML

| Feature        | BML                          | XML                          |
|----------------|------------------------------|------------------------------|
| Format         | Binary                       | Text                         |
| Structure      | Headers with size info + Data| Markup + Content              |
| Attributes     | Not supported                | Supported                    |
| Extensions     | Supported                    | Not supported                |
| Illegal Chars  | None                         | `&`, `<`, `>`                |

## BML Node Structure

A BML document is composed of **nodes**, each containing:
- A **header** (tag, extensions, data size)
- Optional **data**
- Optional **child nodes**
- An **End Of Children (EOC)** marker

### Node Header Fields

| Field      | Size (bytes) | Description                                                                 |
|------------|--------------|-----------------------------------------------------------------------------|
| FH         | 1            | Format Header: describes availability/size of extensions, ID, and data size|
| ID Size    | ISS + 1      | Size of the tag ID (Little-Endian)                                          |
| ID Data    | IS           | Tag identifier                                                               |
| Extensions | >= 0         | Optional auxiliary data                                                      |
| Data Size  | DSS          | Size of data (Little-Endian)                                                 |
| Data       | DS           | User data (optional)                                                         |
| Child Nodes| >= 0         | Nested BML nodes (optional)                                                  |
| EOC        | 1            | End Of Children marker                                                       |

## Libraries

BML provides two libraries for reading/writing and manipulating BML data:

1. **BML_NODE (C++)**: DOM-style API, loads the entire document into memory. Ideal for smaller documents.
2. **SAB (C)**: SAX-style API, streams data for large documents.

Both libraries are part of **libbml**, licensed under **LGPLv3**.

## Installation

### From Source

```bash
git clone https://github.com/seagnal/bml
cd libbml
bash ./autogen.sh
./configure
sudo make install
```

### Python Bindings

```bash
sudo python3 ./setup.py install
```

### Octave/Matlab Bindings

1. Checkout bml library from this Github:
	
	* ``git clone https://github.com/seagnal/bml.git``
2. Run `compile.m` from the `octave` folder in Octave/Matlab.
	* Open Matlab at the bml root directory

	* Move to the directory <*bml root dir*>/*octave* 
		
		``>> cd octave``

	* Run the script *compile_matlab.m*
		
		``>> compile_matlab``

If the mex compiling process completed successfully the following mex functions are generated in the octave directory:

*	*bml_open.mexw64*
*	*bml_read.mexw64*
*	*bml_write.mexw64*
*	*bml_close.mexw64*

3. Run the sample test

* Go to the directory <*bml root dir*>/*octave*/*test*
	
	``>> cd test``

* Run the script test_bml.m 
	
	``>> test_bml``


4. Add the folder to your path using `addpath()`.


### Matlab Mex Compiler Configuration (Optional)

A Visual Studio or MinGW C/C++ compiler installation is required for generating the bml Matlab mex functions. 

The Matlab mex compiler can compile C/C++ code using the Visual Studio and MinGW C++ compilers. 

	
To check which compiler is selected open Matlab and use the command:

``>> mex -setup``
	
To manually select the compilers type the following command line:

For Visual Studio 2017 compiler: 

``>> mex -setup:'<path to the MATLAB dir>\R2020a\bin\win64\mexopts\msvc2017.xml' ``

For MinGW compiler:

``>> mex -setup:'<path to the MATLAB dir>\R2020a\bin\win64\mexopts\mingw64.xml'``


## Usage Examples

### C++ (BML_NODE)

```cpp
#include <bml_node.hh>

// Create a node
node<uint32_t, std::shared_ptr> my_node(1234);
my_node.set_data<uint32_t>(10);

// Add child nodes
my_node(1).set_data<uint32_t>(1);
my_node(2).set_data<uint32_t>(2);

// Write to file
node_file_writer<std::shared_ptr> writer("/tmp/data.bml");
my_node.to_writer(writer);

// Read from file
node<uint32_t, std::shared_ptr> read_node;
node_file_parser<std::shared_ptr> parser("/tmp/data.bml");
read_node.from_parser(parser);
```

### Python

```python
from bml_tools import bml

# Create a node
a = bml.node(10)
a.data = b'data'

# Append child
b = bml.node(11)
b.data = b'child data'
a.append(b)

# Write to file
wr = bml.bml_file('/tmp/test.bml', 'w')
a.to_writer(wr, 0)
wr.close()

# Read from file
rd = bml.bml_file('/tmp/test.bml', 'r')
ar = bml.node()
ar.from_parser(rd)
```

### Octave/Matlab

```matlab
fid = bml_open('test.dat', 'w');
bml_write(fid, struct('id', 1, 'data', 'data1'));
bml_write(fid, struct('id', 2, 'data', uint64(20000)));
bml_close(fid);
```

## Documentation

For more details, see the [full documentation](http://www.gnu-log.net/home/bml).

## License

BML is licensed under **LGPLv2.1**.

