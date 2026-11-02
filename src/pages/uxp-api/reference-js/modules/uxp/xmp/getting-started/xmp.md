---
title: require('uxp').xmp
description: Since: v7.2.0
---

# require('uxp').xmp
**Since**: v7.2.0
\<br\>\</br\>

Adobe's XMP (Extensible Metadata Platform) is a metadata standard developed by Adobe Systems. It allows users to embed descriptive, rights, and other metadata into digital files.
XMP is extensible and customizable, supporting various types of information and user-defined schemas. It is compatible with Adobe software and third-party applications, promoting interoperability.
Based on open standards, XMP follows XML syntax and RDF model. It enhances searchability, organization, and rights management of files. Overall, XMP facilitates efficient metadata management and effective digital asset management.
For more information about XMP metadata, see the [XMP Specification at Adobe Developer Center](https://www.adobe.com/devnet/xmp.html).
\<br\>\</br\>

XMP module in UXP offers JavaScript access to the Adobe XMP Core and XMP Files libraries.
To use the XMP objects, you must import the XMP module.

```js
const xmp = require("uxp").xmp;
```

After importing the module, you can access the top-level objects of the XMP scripting API.
| Class | Description |
| --- | --- |
| [XMPMeta](../xmp-classes/xmp-meta.md) | Provides interface to work with XMP metadata (got via the hosts DOM APIs) for reading, writing and modifying. |
| [XMPFile](../xmp-classes/xmp-file.md) | Provides interface to work with XMP metadata on a file directly, (instead of depending on host to fetch the XMP metadata contents). |
| [XMPUtils](../xmp-classes/xmp-utils.md) | Provides additional utility functions for array handling. |
| [XMPDateTime](../xmp-classes/xmp-date-time.md) | Represents date-time values. |
| [XMPConst](../xmp-classes/xmp-const.md) | Contains numeric and string constant values for use with the JavaScript API. |

The top-level objects provide access to additional support classes that encapsulate individual metadata properties, file information, and XMP packet information, and a utility that allows iteration through properties.
| Class | Description |
| --- | --- |
| [XMPIterator](../xmp-classes/xmp-iterator.md) | Allows iteration through properties in an XMPMeta object. |
| [XMPProperty](../xmp-classes/xmp-property.md) | Describes a metadata property. |
| [XMPFileInfo](../xmp-classes/xmp-file-info.md) | Describes a file. |
| [XMPPacketInfo](../xmp-classes/xmp-packet-info.md) | Describes a raw XMP packet in a file. |

See specific object reference for details of the class, its properties and methods.

## Using the XMP scripting API
The XMPMeta object is the primary means of access to the namespaces and properties of an XMP metadata packet. Through this object, you can create and delete namespaces and properties, and examine and modify property values.
You can obtain or create an XMPMeta object in several ways:

You can use an XMPFile object to retrieve existing metadata directly from a file. The XMPFile.getXMP() method creates an XMPMeta object, which you can use to examine or modify the properties and their values. You can then use XMPFile.putXMP() to write the modified metadata back to the file.

You can create an XMPMeta object with the constructor, initializing it with an XMP packet created or obtained elsewhere.

You can create a new, empty XMPMeta object with the constructor, and use its methods to create entirely new namespaces and properties. You can then use XMPFile.putXMP() to inject the new metadata into a file.

## Creating new metadata
This code creates an empty XMPMeta object, uses it to set a metadata property, and serializes it to a string, which you could pass to an authoring tool, for example, or store in a file.

```js
const { XMPMeta, XMPConst } = require("uxp").xmp;
let xmp = new XMPMeta();
xmp.setProperty(XMPConst.NS_XMP, "CreatorTool", "My Script");
let xmpStr = xmp.serialize(); // Serialize the XMP packet to XML

// Retrieve property
let prop = xmp.getProperty(XMPConst.NS_XMP, "CreatorTool");
console.log( `namespace: ${prop.namespace}, property path + name: ${prop.path}, value: ${prop.value}`);
```

## Modifying existing metadata
This code initializes an XMPMeta object with an existing XMP packet stored in a string variable. It sets the modification-date property to the current date and time, and stores the updated XMP packet back to the string in compact format.

```js
const { XMPMeta, XMPConst, XMPDateTime } = require("uxp").xmp;
let xmpStr = `<x:xmpmeta xmlns:x="adobe:ns:meta/" x:xmptk="Adobe XMP Core 9.1-c001 79.1462899777, 2023/06/25-23:57:14        ">
    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
        <rdf:Description rdf:about=""
                xmlns:xmp="http://ns.adobe.com/xap/1.0/"
                xmlns:dc="http://purl.org/dc/elements/1.1/"
                xmlns:photoshop="http://ns.adobe.com/photoshop/1.0/">
            <xmp:CreatorTool>Adobe Photoshop 25.0 (Macintosh)</xmp:CreatorTool>
            <xmp:CreateDate>2023-09-26T11:30:41+05:30</xmp:CreateDate>
            <xmp:ModifyDate>2023-09-26T11:30:41+05:30</xmp:ModifyDate>
            <xmp:MetadataDate>2023-09-26T11:30:41+05:30</xmp:MetadataDate>
            <dc:format>application/vnd.adobe.photoshop</dc:format>
            <photoshop:ColorMode>3</photoshop:ColorMode>
            <photoshop:ICCProfile>sRGB IEC61966-2.1</photoshop:ICCProfile>
        </rdf:Description>
    </rdf:RDF>
</x:xmpmeta>`;
let xmp = new XMPMeta(xmpStr); // Object initialized with xmp packet as string
let dateTime = new XMPDateTime(new Date()); // Now
let oldModificationDate = xmp.getProperty(XMPConst.NS_XMP, "ModifyDate", XMPConst.XMPDATE);
console.log("Old modification date: " + oldModificationDate);
xmp.setProperty(XMPConst.NS_XMP, "ModifyDate", dateTime, 0, XMPConst.XMPDATE);

// Serialize to XML, in compact style
xmpStr = xmp.serialize(XMPConst.SERIALIZE_USE_COMPACT_FORMAT);
```

## Using XMPFile for batch processing
This example iterates through a folder of image files and processes the metadata. The script processes each picture as follows:

Reads and parses the metadata. If an image file does not contain XMP metadata, the legacy metadata is automatically converted to XMP.

Deletes the list of existing creators, and adds a new creator value.

Writes the modified metadata back to the file.

**Note:** This example shows the recommended way to work with XMP data without opening the files in the host application.

```js
// Load the XMPScript library
const { XMPConst, XMPFile } = require("uxp").xmp;

// Iterate through the photos in the folder
const uxpfs = require("uxp").storage;
const ufs = uxpfs.localFileSystem;

async function xmpBatchProcessing() {
    console.log("XMPFiles batch processing example");
    let folder = await ufs.getFolder({initialDomain: uxpfs.domains.userDocuments});
    if (!folder) {
        return; // Folder picker was cancelled
    }
    let files = await folder.getEntries();
    files.forEach((file) => {
        // Applies only to files, not to folders
        if (file.isFile) {
            console.log("Process file: " + file.name);
            let xmpFile = new XMPFile(file.nativePath, XMPConst.FILE_UNKNOWN, XMPConst.OPEN_FOR_UPDATE);
            try {
                let xmp = xmpFile.getXMP();

                // Delete existing authors and add a new one
                // Other existing metadata stays untouched
                xmp.deleteProperty(XMPConst.NS_DC, "creator");
                xmp.appendArrayItem(XMPConst.NS_DC, "creator", "Judy", 0, XMPConst.ARRAY_IS_ORDERED);

                // Write updated metadata into the file
                if (xmpFile.canPutXMP(xmp)) {
                    xmpFile.putXMP(xmp);
                } else {
                    console.log("Can't update XMP on file", file.nativePath);
                }
            } finally {
                xmpFile.closeFile(XMPConst.CLOSE_UPDATE_SAFELY);
            }
        }
    });
}

// Invoke from an async event handler
// await xmpBatchProcessing();
```

## Photoshop Only - Writing to an active document
This example applies only to **Photoshop**. Do not use it in plugins for other Adobe host apps.

**Note:** This example uses `batchPlay` to get XMP data from the active document in Photoshop and writes the modified metadata inside `executeAsModal`.

```js
// BatchPlay commands to get and set XMP
const { XMPMeta, XMPConst } = require("uxp").xmp;
const bp = require("photoshop").action.batchPlay;
const psCore = require("photoshop").core;

const getDocumentXMP = async () => {
    const result = await bp(
        [
            {
                _obj: "get",
                _target: [
                    { _property: "XMPMetadataAsUTF8" },
                    { _ref: "document", _enum: "ordinal", _value: "targetEnum" },
                ],
            },
        ],
        { synchronousExecution: false }
    );
    return result[0].XMPMetadataAsUTF8;
};

const setDocumentXMP = async (xmpString) => {
    await psCore.executeAsModal(
        async () => {
            await bp(
                [
                    {
                        _obj: "set",
                        _target: [
                            { _property: "XMPMetadataAsUTF8" },
                            { _ref: "document", _enum: "ordinal", _value: "targetEnum" },
                        ],
                        to: {
                            _obj: "XMPMetadataAsUTF8",
                            XMPMetadataAsUTF8: xmpString,
                        },
                    },
                ],
                { synchronousExecution: false }
            );
        },
        { commandName: "Setting XMP..." }
    );
};

function modifyXMP(xmp) {
    xmp.setLocalizedText(XMPConst.NS_DC, "title", "", "x-default", "Sample Title");
    xmp.appendArrayItem(XMPConst.NS_DC, "creator", "Sample Creator Value", 0, XMPConst.ARRAY_IS_ORDERED);
    xmp.setLocalizedText(XMPConst.NS_DC, "description", "", "x-default", "Set description Here");
    xmp.setProperty(XMPConst.NS_XMP, "Rating", "4"); // Rating Number
    xmp.setProperty(XMPConst.NS_PHOTOSHOP, "AuthorsPosition", "Sample Author Position");
    xmp.setProperty(XMPConst.NS_PHOTOSHOP, "CaptionWriter", "Sample Caption Writer");
    return xmp;
}

async function updateXMP() {
    try {
        const xmpData = await getDocumentXMP();
        const meta = modifyXMP(new XMPMeta(xmpData));
        await setDocumentXMP(meta.serialize());
    } catch (error) {
        console.error(error);
    }
}

// Create an HTML button with id="UpdatePSFileInfo" before registering this handler
document.getElementById("UpdatePSFileInfo").addEventListener("click", updateXMP);
```

