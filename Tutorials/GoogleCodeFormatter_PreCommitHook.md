# How To Add Code formatter and run it as Pre Commit hook

Code formatter used - Google Sytle Formatter (https://github.com/google/google-java-format)

##### Implementation:

- Download the jar file for the appropriate java version. Make sure you download the jar file with google-java-format-<version>-**all-deps**.jar
- To run one of the files to check how the the file will look after formatting:
  
      jave -jar <path to jar file> <file location>/<filename>.java
  This will show you how the file will look like after formatting

- To run jar file against every commit and then to replace the existing file with formatted file:
  - create a folder .githook on the root level code directory. If you have .git folder you can use that
  - create a file pre-commit.sh
  
        #!/bin/bash
        GOOGLE_STYLE_FORMATTER=<path to jar file>/google-java-format-1.7-all-deps.jar
        
        if [[ ! -f $GOOGLE_STYLE_FORMATTER ]]
        then
           echo 'Google Style formatting jar file missing'
        fi

        changed_java_files=$(git diff --cached --name-only --diff-filter=ACMR | grep ".*java$" || true)
        echo "New or changed files: $changed_java_files"
        
        if [[ -n "$changed_java_files" ]]
        then
        reformatted_files=$(java -jar $GOOGLE_STYLE_FORMATTER --dry-run $changed_java_files | tr '\r\n' ' ')
        
            if [[ -n "$reformatted_files" ]]
            then
                echo "Formatted files: $reformatted_files"
                java -jar $GOOGLE_STYLE_FORMATTER --replace $reformatted_files
                echo "Re-adding formatted files: $reformatted_files"
                git stage $reformatted_files
            fi
        else
        echo "No Java files changes found."
        fi

  - Note if you are running and committing code using windows machine then path to your jar file will look something like this when placed in C:/ drive:
    /c/Users/CodeFormatting/google-java-format-1.7-all-deps.jar
    
  - To configure pre commit hook file to run everytime when you commit, run following from terminal inside your code directory:
    
        git config --local core.hooksPath .githook/
  
