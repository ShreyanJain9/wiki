## DO NOT VALIDATE @CONTEXT IF YOU DO NOT UNDERSTAND JSON-LD

you CANNOT check for the presence of `https://www.w3.org/ns/activitystreams` AT ALL -- the activitystreams context may be included within another context hosted elsewhere. or it may be excluded (although it SHOULD NOT). just ignore this property entirely if you don't understand it