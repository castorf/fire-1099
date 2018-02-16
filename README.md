# What this is
Fire-1099 helps you generate 1099 tax filings formatted for the IRS electronic filing system.

A lot of small companies don't realize they have to file 1099s for most payments to lawyers, as well as independent contractors. The IRS has a system called "FIRE" for electronically submitting these filings, and others like stock options exercise forms. These filings can *only* be filed through this system. If you're used to modern REST APIs, you'll probably find FIRE hard to use. It's inflexible, has an ambiguous spec, and operates on the byte (ASCII code) level. 

With fire-1099, you simply enter your form data in a JSON file [like this one](http://www.github.com) and run it through the program. It validates your data against the IRS spec, auto-formats it where possible, and writes it to a file that can be uploaded straight to FIRE.

I should point out getting access to the FIRE system is non-trivial; it can take a couple of weeks. See below for a link to the form needed.

# Using fire-1099
The CLI for generating FIRE-formatted files accepts two basic parameters: an input file path and an output file path.


`fire-1099 path/to/input-file.json --output path/to/output-file.ascii`


The input file should be JSON-formatted according to the schema defined in the `/schema` folder of this repo. The output file given by `--output` is optional, and will default to a timestamped filename in the same directory as the input file. Not all fields in the input file are required. I recommend using the file `/spec/data/valid-minimal.json` as a starting point if you're not comfortable with the schema file itself.


## API (Translator Module)
As an alternative to the CLI, the `translator` module exposes a number of functions for generating FIRE-formatted files programatically.


To run the file generation process end-to-end (similar to using the CLI), use `translator.run(str, str)`. Example:

```
import translator

input_path = "/path/to/input_file.json"
output_path = "/path/to/output_file.ascii"

translator.run(input_path, output_path)
```


A more step-by-step interaction is also available:

```
import translator

input_path = "/path/to/input_file.json"
output_path = "/path/to/output_file.ascii"

# Load input file and validate against schema
user_data = extract_user_data(input_path)
validate_user_data(user_data, schema_path)

# Incorporate default values and system-generated data
master = load_full_schema(user_data)
insert_generated_values(master)

# Generate ASCII string formatted to IRS 1220 spec, and write to file
ascii_string = get_fire_format(master)
write_1099_file(ascii_string, output_path)
```


# Access via IRS FIRE System
A few things need to happen before you can submit an output file to the IRS:

* You need a *Transmitter Control Code* or "TCC." To get one, you unfortunately have to mail or fax [this form](https://www.irs.gov/pub/irs-pdf/f4419.pdf). 
* You need to have a valid business tax identification code (EIN/TIN). This will be linked to your TCC, and is what you'll use for the "transmitter" record in your FIRE submissions.

# Future Work
* Add support for "Extension of Time" requests
* Add support for filings other than 1099-MISC
* Improve schema regex validations
* Add validation logic for more obscure fields, and for cross-field dependencies like "combined state-federal"
* Add support for multiple sequential files, i.e. scope sequence numbers to multiple files


Note: support multiple payers in a single file is not planned, as this is meant as a "DIY" tool for businesses to use themselves.


