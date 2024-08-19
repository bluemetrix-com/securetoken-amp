## Securing Sensitive Data in CDP with Bluemetrix

SecureToken is a Spark-based solution that makes data tokenization effortless within the Cloudera Data Platform (CDP). Fully integrated and compliant with NIST and FIPS 140-3, it securely tokenizes and detokenizes data without the data leaving the LakeHouse. It supports a wide range of use cases, GDPR compliance, ML/analytics, secure data sharing, and more.

![alt text](images/image2.png "SecureToken AMP")

This repo shows you how to quickly leverage Cloudera Machine Learning (CML) to securely tokenize data, scale effortlessly, and use this tokenization capability in your existing data pipelines - in hours, not days or weeks.

## AMP Architecture
![alt text](images/image3.png "AMP Architecture")
## Bluemetrix SecureToken AMP Overview 
This AMP will show how Bluemetrix SecureToken provides a simple process for tokenizing and detokenizing data when using CML to help protect sensitive data.

It includes a Jupyter notebook that provides examples of how the tokenization and detokenization of the data works.

## Prerequisites
- CML Session Editor should be JupyterLab
- Bluemetrix SecureToken ML Runtime is required

## Installation

SecureToken is based on a customized ML Runtime Image and its content is stored on a private Gitlab repository.

To access the private repository, a request should be sent to the e-mail **securetoken@bluemetrix.com**. Access will be allowed to the private Gitlab Docker Registry (to download the ML Runtime) and to the Gitlab Repository (to clone the AMP contents).

Follow the [standard instructions](https://docs.cloudera.com/machine-learning/cloud/runtimes/topics/ml-registering-customized-runtimes.html) to register the ML Runtime, including the [step](https://docs.cloudera.com/machine-learning/cloud/runtimes/topics/ml-add-docker-registry-credentials-runtimes.html) to register the Docker Registry credentials.

Once the ML Runtime is registered and available, follow the [standard instructions](https://docs.cloudera.com/machine-learning/cloud/applied-ml-prototypes/topics/ml-amp-add-catalog.html) for installing this AMP into your CML workspace. Select the option “**Git Repository URL**” and enter the URL: https://github.com/bluemetrix-com/securetoken-amp/catalog.yaml and the "**File Name**" to use is `catalog.yaml`,

When deploying your session you will need to select the custom ML Runtime named Bluemetrix SecureToken.

If you encounter any issues with installing the AMP, Bluemetrix will be happy to provide a consultant to talk you through the process.

he "File Name" to use is catalog.yaml,

When deploying your session you will need to select the custom ML Runtime named Bluemetrix SecureToken.

If you encounter any issues with installing the AMP, Bluemetrix will be happy to provide a consultant to talk you through the process.

## SecureToken Jupyter Notebook

The notebook included in the AMP will cover the tokenization/detokenization of data for Call Records and Subscriber Summary CSV file.

To use the notebook, a new session using the Bluemetrix SecureToken ML Runtime should be used with the editor JupyterLab: 

### Sections

The SecureToken Jupyter Notebook includes the following sections:

1. **Setup the Spark Session**: This section will start the Spark Session

2. **Initialize The Tokenization Instance With A Locally Stored Key**: will initialize the SecureToken instance with the encryption key included in the project

3. **Read Sample Data from Workspace**: will create a dataframe based on the Call Records CSV file

4. **Show Top5 Destination Numbers**: will show the 5 top destination numbers on original format

5. **Show Top5 Destination Across Original, Tokenized and Detokenized Data Sets**: will show the 5 top destinations across three different charts (Original, Tokenized and Detokenized)

6. **Save Call Records CSV with Tokenized destination to compare De-Tokenization / Original execution time**: Save a new CSV file named tok_call_log.csv with the Call Records data including the destination field tokenized.

7. **Show Execution Time Original vs Tokenized**: will show a chart comparing the execution time when working with data from a CSV file with the original data and the execution time when working with data from a CSV with tokenized data (which includes the detokenization stage)

8. **Show Original, Tokenized & Detokenized Destination Values Side By Side**: will show a table with the 5 top destination numbers as seen on the original format, tokenized format and detokenized format. This example shows how SecureToken is able to retrieve the original data back.

9. **Show Top 5 Origin Numbers**: will show the 5 top origin numbers on original format

10. **Show Top 5 Origin Numbers Across Original, Tokenized & Detokenized Data Sets**: will show the 5 top origin across three different charts (Original, Tokenized and Detokenized)

11. **Show Original, Tokenized & Detokenized Origin Values Side By Side**: will show a table with the 5 top origin numbers as seen on the original format, tokenized format and detokenized format. This example shows how SecureToken is able to retrieve the original data back.

12. **Load Secondary Dataset**: will create a second dataframe based on the Subscriber Summary CSV file

13. **Join Secondary Dataset with Top 5 Origin Numbers**: join the two dataframes using the origin number on original format. Shows the content of the 5 top numbers.

14. **Tokenize Both Data Sets and Recreate View**: shows a table with the top 5 origin numbers (similar to the previous item), but with the origin and current_ip columns tokenized.

15. **Detokenize The Complete Analysis Above**: will detokenize the data shown on the previous item and show that the detokenized data is retrieved with this process.

16. **Save Call Records CSV with Tokenized origin to compare De-Tokenization / Original execution time**: Save a new CSV file named origin_tok_call_log.csv with the Call Records data including the destination field tokenized.

17. **Save Subscriber Summary CSV with Tokenized origin  and current_ipto compare De-Tokenization / Original execution time**: Save a new CSV file named origin_ip_tok_sub_summary.csv with the Call Records data including the destination field tokenized.

18. **Show Execution Time Original vs Tokenized for joining datasets**: will show a chart comparing the execution time when working with data from a CSV file with the original data and the execution time when working with data form a CSV with tokenized data (which includes the detokenization stage)

19. **Show Joined Execution Time Original vs Tokenized**: will show a chart comparing the execution time when working with data from CSV files with the original data (both Call Records and Subscriber Summary) and the execution time when working with data from CSVs with tokenized data (which includes the detokenization stage)

20. **Summarize Tokenized & Detokenized Joined Data in a Graph**: will show two charts with the summarized data from the previous items.

## Using different Data Sets
Bluemetrix SecureToken supports various data sets, allowing users to easily upload their own files, such as CSV, to the Jupyter session for Tokenization.

## Tokenization Minimum Size and leading zeros

Since values will be tokenized with format-preserving encryption, it requires a minimum number of possibilities (1,000,000). This number is calculated by X^Y > 1,000,000, where X is the number of characters in the alphabet, and Y is the desired input length. For example, in the case of the numeric alphabet, the minimum length required is 6 (10^6), as the numeric alphabet has 10 possibilities (0-9).

The underlying tokenization algorithm treats everything as a string and requires you to specify the alphabet associated with the string. We can provide different alphabets to achieve different functionality. For example, providing an alphabet with the character “0123456789” will allow us to tokenize numbers such as credit cards or phone numbers. However, there can be some caveats. If you wish to store the output as an actual numeric type, any leading zeros will be dropped and could break detokenization. In such cases, if you knew that all values are of a fixed length, you could write a routine to support this use case.

One example of using fixed-length logic is when dealing with IPv4 addresses. In order to stay within valid IP ranges, all octets are first converted to binary form and padded to be a fixed length of 8 before being concatenated into a single 32-character binary representation. Tokenization/detokenization is then called with a binary alphabet and the reverse logic is applied to convert this new value back into an IPv4 string. 

Other examples would include datasets like credit cards, phone number, e-mails, etc and these will be added to the AMP library over the coming weeks and months.

## The Fine Print

IMPORTANT: Please read the following before proceeding. This AMP includes or otherwise depends on certain third-party Python packages. Information about such third-party software packages is made available in the notice file associated with this AMP. By configuring and launching this AMP, you will cause such third party python packages to be downloaded and installed into your environment from their respective public repositories. For each third-party python package, please see the notice file and the applicable websites for more information, including the applicable license terms.

If you do not wish to download and install the third-party Python packages, do not configure, launch or otherwise use this AMP. By configuring, launching or otherwise using the AMP, you acknowledge the foregoing statement and agree that Cloudera and Bluemetrix are not responsible or liable in any way for the third-party python packages.