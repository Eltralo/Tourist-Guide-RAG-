# Tourist-Guide-RAG-

This is RAG for tourists (from MFTI) here we use Colbert and Qween.

About data:

dataset containing information about monuments, architecture, and other significant places. 

The data contains:

* name of the attraction;
* the identifier on WikiData;
* Location city;
* longitude and latitude coordinates;
* description (extracted from WikiData);
* image in base64 format;
*  image description (generated using the BLIP model).
*  
Some of the monuments may occur several times, but with different images, not all of which may be of high quality. Text descriptions are provided to compensate for this.
