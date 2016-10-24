# dig-extractor
![travis ci](https://travis-ci.org/usc-isi-i2/dig-extractor.svg?branch=master)

`dig-extractor` is a generic framework for writing Extractors that can be plugged into the Karma Information Integration Workflows.

To implement an Extractor that can be used by the Workflows:

* Implement the `Extractor` class in dig-extractor.  The `ExtractorProcessor` will wrap the extractor and take care of a bunch of boilerplate code and allow us to chain `Extractors`.

* When you implement the `Extractor`, the business logic goes in the `extract` function.  It expects a dictionary and returns the extracted value.  Usually it’s just a string, but it can be an array or a dictionary. https://github.com/usc-isi-i2/dig-extractor/blob/master/test/test_extractor.py#L29

* You define the expected value(s) for the input dictionary in `renamed_input_fields` https://github.com/usc-isi-i2/dig-extractor/blob/master/test/test_extractor.py#L26

* The `ExtractorProcessor` will take the values from the original document to match the expected `renamed_input_fields` (in order) when you call `set_input_fields` https://github.com/usc-isi-i2/dig-extractor/blob/master/test/test_extractor.py#L90

* The input_fields will actually accept jsonpaths, so you can access nested values and filter on them.  

* If you have more than one output field (the extractor creates a dictionary), you can either rename the fields (https://github.com/usc-isi-i2/dig-extractor/blob/master/test/test_extractor.py#L156) or just filter the fields https://github.com/usc-isi-i2/dig-extractor/blob/master/test/test_extractor.py#L172

Here is the code of a sample extractor that extracts emails
```
class EmailExtractor(Extractor):

    def __init__(self):
        self.renamed_input_fields = ['text']

    def extract(self, doc):
        if 'text' in doc:
            return DIGEmailExtractor(_output_format='obfuscation').extract_email(doc['text'])
        return None

    def get_metadata(self):
        return copy.copy(self.metadata)

    def set_metadata(self, metadata):
        self.metadata = metadata
        return self

    def get_renamed_input_fields(self):
        return self.renamed_input_fields
```


We have several readily available extractors that you can use for reference:

1. Extractor for emails - https://github.com/usc-isi-i2/dig-email-extractor

2. Extractor for age - https://github.com/usc-isi-i2/dig-age-extractor

3. Extractor for phone - https://github.com/usc-isi-i2/dig-phone-extractor
