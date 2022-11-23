class Ed 
    def initialize
        @current = 0 # カレント行
        @buffer = [] # バッファ
        @file = nil # ファイル
        loop {
            _print(_eval(_read))
        }
    end

    def _read
        @str = STDIN.gets #読み込み
    end

    def _eval # 正規表現マッチしたかどうか
        @addr = "(?:\d+|[.$,;]|\/.*\/)"
        @cmnd = "(?:wq|[acdfijnpqrw=]|\z)"
        @prmt = "(?:.*)"
        @is_match = /\A(#{@addr}(?:,#{@addr})?)?(#{@cmnd})(#{@prmt})?\z/
        if /(?:#{@is_match})/ =~ @str then
            print "ok" #デバッグ
            self.send("command_#{@cmnd}")
        else
            _print
        end
    end

    def _print #エラー
        print "?"
    end

    def command_a
        if $1
            @buffer.insert($1+1, 〜)
        else
            @buffer.insert(@current+1, 〜) 
        end 
        @current = @current+1
    end

    def command_c
        command_d
        command_a
    end

    def command_d
        @buffer.slice!($1, $2)
        @current = $1
    end

    def command_f
        @file = $4
    end

    def command_i
        if $1
            @buffer.insert($1, 〜)
        else
            @buffer.insert(@current, 〜)
        end
        @current = @current
    end

    def command_j
        # joinする
    end

    def command_n
        # 指定した範囲と行番号表示
        if $1 and $2 then
            ($1..$2).each do |i|
                print "#{i}: #{@buffer[i]}"
            end
        else
            _print
        end
    end

    def commpand_p
        # 指定した範囲表示
        if $1 and $2 then
            ($1..$2).each do |i|
                print @buffer[i]
            end
        else
            _print
        end
    end

    def command_q
        exit
    end

    def command_w
        @file = $4
        IO.write(@file, @buffer)
        # ファイルに書き込みたい
    end

    def command_wq
        command_w
        command_q
    end

    def command_=
        # print 行番号
        # 指定された行番号表示したい
    end

    def command_
        # イプシロン（改行）
    end
end

Ed.new

