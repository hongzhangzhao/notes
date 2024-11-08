

查看Perl信息
```bash
which -a perl
perl -E 'say for @INC'
perl -MCarp -e 'print $Carp::VERSION'
perl -MCarp -e 'print $INC{"Carp.pm"}'  # 查看carp位置
```