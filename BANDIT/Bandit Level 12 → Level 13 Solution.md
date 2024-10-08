# Bandit Level 12 → Level 13 Solution

## Objective

The goal for Bandit Level 12 → Level 13 was to extract the password from a file named `data.txt`, which was a hexdump of a file that had been repeatedly compressed. The challenge required reversing the compression steps to reveal the password.

## Solution Steps

1. **Create a Temporary Working Directory:**

   We started by creating a temporary directory to work in, using the command:

   ```bash
   tmp_dir=$(mktemp -d)
   cd $tmp_dir
2. **Copy the `data.txt` file to the temp dir**
  cp ~/data.txt $tmp_dir
  cd $tmp_dir

3. **Reverse the Hexdump**
  xxd -r data.txt > data.bin

4. **Identify the Compression Type**
  file data.bin

5. **Decompress the File**
  mv data.bin data.gz
  gunzip data.gz

6. Continue Decompressing:
  tar xvf data

## This extraction process revealed several more files, each needing similar processing (decompression and extraction).

7. Final Decompression:

After several iterations of decompressing files, we finally extracted a file named data8, which contained ASCII text.

cat data8 = The password is "redacted"
