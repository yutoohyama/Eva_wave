# Eva_wave
wave作成
参照したサイト「Numpyで奏でるクリスマスソング　https://qiita.com/TatchNicolas/items/fb4064a7f394b977765e　」 を元にwaveを作成していく。
以下コード


# とりあえず音を鳴らしてみる

    import numpy as np
    import IPython.display 

    BIT_RATE = 44100
    concert_pitch = 220

    A3 = concert_pitch 

        # ピアノでいう、いわゆる「ラ」の音

    duration = 3
    sample_A3 = np.sin(A3 * 2 * np.pi * np.linspace(0, duration, duration * BIT_RATE))
    IPython.display.Audio(sample_A3, rate=BIT_RATE)


        ratios = [2 ** (i / 12) for i in range(0, 13)]
        tone_names = ['A3', 'As3', 'B3', 'C4', 'Cs4', 'D4', 'Ds4', 'E4', 'F4', 'Fs4', 'G4', 'Gs4','A4' ]

        TONE_DICT = {}

            # A3からA4のクロマチックスケール
    
        for ratio, tone_name in zip(ratios, tone_names):
            duration = 1
            frequency = ratio * A3
            tmp_sound = np.sin(frequency * 2 * np.pi * np.linspace(0, duration, duration * BIT_RATE))
            audio=IPython.display.Audio(tmp_sound, rate=BIT_RATE)

            # あとで使えるように辞書に周波数を入れとく
            TONE_DICT[tone_name] = frequency
            # Jupyter上に表示
            print(f'tone_name:{tone_name}, frequency: {frequency}')
            IPython.display.display(audio)


            # 1オクターブ上、1オクターブ下、2 オクターブ下も作る

            # さきほどの1オクターブ分の周波数を作る処理を関数化

        def generate_octave(start, tone_names):
            dict_to_return = {}
            for ratio, tone_name in zip(ratios, tone_names):
                frequency = ratio * start
                dict_to_return[tone_name] = frequency
            return dict_to_return

            # A2からA3のクロマチックスケール

        tone_names_a2_a3 = [ 'A2', 'As2', 'B2', 'C3', 'Cs3', 'D3', 'Ds3', 'E3', 'F3', 'Fs3', 'G3', 'Gs3', 'A3']
        a2_a3 = generate_octave(TONE_DICT['A3'] / 2, tone_names_a2_a3)  # 基準音(440Hz)の半分の周波数 = 1オクターブ下の音から始める
        TONE_DICT.update(a2_a3)

            # A1からA2のクロマチックスケール

        tone_names_a1_a2 = [ 'A1', 'As1', 'B1','C2', 'Cs2', 'D2', 'Ds2', 'E2', 'F2', 'Fs2', 'G2', 'Gs2', 'A2']
        a1_a2 = generate_octave(TONE_DICT['A3'] / 4, tone_names_a1_a2)  # 基準音(440Hz)の1/4周波数 = 2オクターブ下の音から始める
        TONE_DICT.update(a1_a2)

            # A4からA5のクロマチックスケール

        tone_names_a4_a5 = [ 'A4', 'As4', 'B4','C5', 'Cs5', 'D5', 'Ds5', 'E5', 'F5', 'Fs5', 'G5', 'Gs5', 'A5']
        a4_a5 = generate_octave(TONE_DICT['A3'] * 2, tone_names_a4_a5)  # 基準音(440Hz)の4倍の周波数 = 2オクターブ上の音から始める
        TONE_DICT.update(a4_a5)


    # 音価(音の長さ)をつくる

QUATER = 1/2
EIGHTH =  QUATER / 2
SIXTEENTH =  EIGHTH / 2


    # 周波数と音価からsin波として音を作る関数
def generate_note(frequency, note_value):
    one_sec_note = np.sin(TONE_DICT[frequency] * 2 * np.pi * np.linspace(0, 2, 2 * BIT_RATE))
    return one_sec_note[0:int(len(one_sec_note) * note_value)]

#%%

    # 配列を結合する = 音を楽譜で横に並べる = メロディ

c3v4 = generate_note('C3', QUATER)
d3v4 = generate_note('D3', QUATER)
e3v4 = generate_note('E3', QUATER)
f3v4 = generate_note('F3', QUATER)

frog = np.hstack((c3v4, d3v4, e3v4, f3v4, e3v4, d3v4, c3v4))
IPython.display.Audio(frog, rate=BIT_RATE)

#%%

    # 配列の和 = 音を楽譜で縦に並べる = ハーモニー

c3v2 = generate_note('C3', QUATER * 2)
e3v2 = generate_note('E3', QUATER * 2)
g3v2 = generate_note('G3',QUATER * 2)

c_triad_chord = np.vstack((c3v2, e3v2, g3v2))
IPython.display.Audio(c_triad_chord, rate=BIT_RATE)

# 残酷な天使のテーゼ～エヴァンゲリオン～

#%%

melody_notes = [
    ('C4', QUATER), ('Ds4', QUATER), ('F4', EIGHTH + SIXTEENTH), ('Ds4', EIGHTH + SIXTEENTH), ('F4', EIGHTH),
    # ドミsファミsファ ざんこくな
    ('F4', EIGHTH), ('F4', EIGHTH), ('As4', EIGHTH), ('Gs4', EIGHTH), ('G4', SIXTEENTH), ('F4', EIGHTH), ('G4', QUATER + SIXTEENTH),
    # ファファシsラSソファソ てんしのように
    ('G4', QUATER), ('As4', QUATER), ('C5', EIGHTH + SIXTEENTH), ('F4', EIGHTH + SIXTEENTH), ('Ds4', EIGHTH),
    # ソシsドファミs しょうねんよ 
    ('As4', EIGHTH), ('As4', EIGHTH),('G4', EIGHTH), ('As4', EIGHTH), ('As4', EIGHTH + SIXTEENTH), ('C5', QUATER) , ('C5', QUATER) , ('C5', QUATER), 
    # シsシsソシsシsド しんわになれ
    
    ('C4', EIGHTH), ('Ds4', EIGHTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH),
    
    ('F4', SIXTEENTH), ('F4', SIXTEENTH), ('As4', SIXTEENTH), ('Gs4', SIXTEENTH), ('G4', SIXTEENTH/2), ('F4', SIXTEENTH), ('G4', EIGHTH + SIXTEENTH/2),
    
    ('G4', EIGHTH), ('As4', EIGHTH), ('C5', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH),
    
    ('As4', SIXTEENTH), ('As4', SIXTEENTH),('G4', SIXTEENTH), ('As4', SIXTEENTH), ('As4', SIXTEENTH + SIXTEENTH/2), ('C5', QUATER), 
    
         
    ('Ds4', SIXTEENTH), ('As3',SIXTEENTH/2), ('As3', EIGHTH + SIXTEENTH),
    ('Ds4', SIXTEENTH), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('As3', SIXTEENTH), ('As3', EIGHTH + SIXTEENTH),
    # ミsシsシsミsミsファシsシs あおいかぜがいま
    ('As3', SIXTEENTH), ('G4', SIXTEENTH + SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2),
    ('F4', SIXTEENTH), ('G4', SIXTEENTH + SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH), ('C4', EIGHTH + SIXTEENTH),
    # シsソラsソファミsファソラsソド むねのどあをたたいても

    ('C4', SIXTEENTH/2), ('D4', SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH), ('D4', EIGHTH + SIXTEENTH), 
    ('Ds4', SIXTEENTH/2), ('F4', SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH), ('Ds4', EIGHTH + SIXTEENTH), 
    # ドレミsミsレレミsファラsソファミs わたしだけをただみつめて

    ('G4', SIXTEENTH), ('G4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('E4', SIXTEENTH), ('F4', EIGHTH), 
    ('C4', EIGHTH), ('C4', EIGHTH + SIXTEENTH), ('D4', SIXTEENTH), ('D4', QUATER + EIGHTH),
    # ソソファミファドドレレ ほほえんでるあなた

    ('Ds4', SIXTEENTH), ('As3',SIXTEENTH/2), ('As3', EIGHTH + SIXTEENTH),
    ('Ds4', SIXTEENTH), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('As3', SIXTEENTH), ('As3', EIGHTH + SIXTEENTH),
    # ミsシsシsシsミsミsファシsシs そっとふれるもの

    ('As3', SIXTEENTH), ('G4', SIXTEENTH + SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2),
    ('F4', SIXTEENTH), ('G4', SIXTEENTH + SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH), ('C4', EIGHTH + SIXTEENTH),
    # シsソラsソファミsファソラsソド もとめることにむちゅうで

    ('C4', SIXTEENTH/2), ('D4', SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH), ('D4', EIGHTH + SIXTEENTH), 
    ('Ds4', SIXTEENTH/2), ('F4', SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH), ('Ds4', EIGHTH + SIXTEENTH), 
    # ドレミsミsレレミsファラsソファミs うんめいさえまだしらない 

    ('G4', SIXTEENTH), ('G4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('E4', SIXTEENTH), 
    ('F4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH + SIXTEENTH/2), ('Gs4', SIXTEENTH), ('G4', QUATER + QUATER),
    # ソソファミファソラsソ いたいけなひとみ 

]

melody_wave = np.hstack([generate_note(tone, value) for tone, value in melody_notes])
Eva1_audio = IPython.display.Audio(melody_wave, rate=BIT_RATE)
IPython.display.display(Eva1_audio)

#%%

melody_notes = [
    ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH),
    ('F4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH), ('D4', SIXTEENTH + SIXTEENTH/2), ('C4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH),
    # ミsミsレミsミsレファファミsレドレ だけどいつかきづくでしょう

    ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH + SIXTEENTH/2), ('C4', SIXTEENTH), ('As3', QUATER + QUATER),
    # ミsミsレファレドシs そのせなかには

    ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('D4', SIXTEENTH),
    ('F4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH),('D4', SIXTEENTH + SIXTEENTH/2),('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH),
    # ミsミsレミsミsレファファミsレミsファ はるかみらいめざすための 

    ('G4', SIXTEENTH + SIXTEENTH/2), ('Gs4', SIXTEENTH + SIXTEENTH/2), ('G4', SIXTEENTH), 
    ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH), ('G4', QUATER + QUATER),
    # ソラsソファミsファソ はねがあること

    ('C4', EIGHTH), ('Ds4', EIGHTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH),
    ('F4', SIXTEENTH), ('F4', SIXTEENTH), ('As4', SIXTEENTH), ('Gs4', SIXTEENTH), ('G4', SIXTEENTH/2), ('F4', SIXTEENTH), ('G4', EIGHTH + SIXTEENTH/2),
    # ドミsファミsファファファシsラsソファソ ざんこくなてんしのてーぜ 

    ('G4', EIGHTH), ('As4', EIGHTH), ('C5', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH), ('D4', SIXTEENTH), 
    ('D4', SIXTEENTH), ('C4', SIXTEENTH), ('D4', SIXTEENTH), ('F4', SIXTEENTH/2), ('Ds4', SIXTEENTH), ('Ds4', EIGHTH + SIXTEENTH/2), 
    # ソシsドファミsレレドレファミsミs まどべからやがてとびたつ
    
    ('C4', EIGHTH), ('Ds4', EIGHTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH),
    ('F4', SIXTEENTH), ('F4', SIXTEENTH), ('As4', SIXTEENTH), ('Gs4', SIXTEENTH), ('G4', SIXTEENTH/2), ('F4', SIXTEENTH), ('G4', EIGHTH + SIXTEENTH/2),
    # ドミsファミsファファファシsラsソファソ ほとばしるあついぱとすで
    
    ('G4', EIGHTH), ('As4', EIGHTH), ('C5', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH),
    ('As4', SIXTEENTH), ('As4', SIXTEENTH),('G4', SIXTEENTH), ('As4', SIXTEENTH), ('As4', SIXTEENTH + SIXTEENTH/2), ('C5', EIGHTH + SIXTEENTH/2),
    # ソシsドファミsシsシsソシsシsド おもいでをうらぎるなら
    
    ('C4', EIGHTH), ('Ds4', EIGHTH), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH),
    ('F4', SIXTEENTH), ('F4', SIXTEENTH), ('As4', SIXTEENTH), ('Gs4', SIXTEENTH), ('G4', SIXTEENTH/2), ('F4', SIXTEENTH), ('G4', EIGHTH + SIXTEENTH/2),
    # ドミsファミsファファファシsラsソファソ このそらをだいてとびたつ
    
    ('G4', EIGHTH), ('As4', EIGHTH), ('C5', SIXTEENTH + SIXTEENTH/2), ('F4', SIXTEENTH + SIXTEENTH/2), ('Ds4', SIXTEENTH),
    ('As4', SIXTEENTH), ('As4', SIXTEENTH),('G4', SIXTEENTH), ('As4', SIXTEENTH), ('As4', SIXTEENTH + SIXTEENTH/2), ('C5', QUATER) 
    # ソシsドファミsシsシsソシsシsド しょうねんよしんわになれ  

]

melody_wave = np.hstack([generate_note(tone, value) for tone, value in melody_notes])
Eva2_audio = IPython.display.Audio(melody_wave, rate=BIT_RATE)
IPython.display.display(Eva2_audio)

#%%

melody_notes = [
('G4', EIGHTH), ('F4', EIGHTH), ('C5', QUATER), ('C5', QUATER + QUATER), 
('G4', EIGHTH), ('F4', EIGHTH), ('C5', EIGHTH + SIXTEENTH), ('As4', QUATER + QUATER),
    # ファリヤーセタメソ ソファドソファドシs
('G4', EIGHTH), ('F4', EIGHTH), ('C5', QUATER + EIGHTH), ('As4', EIGHTH), ('D5', QUATER + QUATER)
    # ファリヤートゥセー ソファドシsレ
]

melody_wave = np.hstack([generate_note(tone, value) for tone, value in melody_notes])
Eva3_audio = IPython.display.Audio(melody_wave, rate=BIT_RATE)
IPython.display.display(Eva3_audio)

#%%

    # バッキングはまだ上手くいっていない

#backing_notes = [
    #('C3', QUATER), ('C3', QUATER), ('F2', QUATER), ('F2', QUATER), 
    #('As2', QUATER), ('As2', QUATER), ('Ds2', QUATER), ('Ds2', QUATER),
    # ドドファファシsシsミsミs ざんこくなてんしのように

    #('C3', QUATER), ('C3', QUATER), ('F2', QUATER), ('F2', QUATER), 
    #('As2', QUATER), ('As2', QUATER), ('Gs2', QUATER), ('Gs2', QUATER), ('Gs2', QUATER), ('Gs2', QUATER),
    # ドドファファシsシsソsソsソsソs しょうねんよしんわになれ

    #('C3', EIGHTH), ('C3', EIGHTH), ('F2', EIGHTH), ('F2', EIGHTH), 
    #('As2', EIGHTH), ('As2', EIGHTH), ('Ds2', EIGHTH), ('Ds2', EIGHTH),
    

    #('C3', EIGHTH), ('C3', EIGHTH), ('F2', EIGHTH), ('F2', EIGHTH), 
    #('As2', EIGHTH), ('As2', EIGHTH), ('Gs2', EIGHTH), ('Gs2', EIGHTH),

    #('Ds2', EIGHTH), ('Ds2', EIGHTH), ('Ds2',EIGHTH), ('Ds2', EIGHTH), 
    #('As3', EIGHTH), ('As3', EIGHTH), ('As3', EIGHTH), ('As3', EIGHTH),
    # ミsミsミsミsシsシsシsシs あおいかぜがいま

    #('C3', EIGHTH), ('C3', EIGHTH), ('As3', EIGHTH), ('As3', EIGHTH),
    #('Gs3', EIGHTH), ('Gs3', EIGHTH), #('Gs3', EIGHTH), ('Gs3', EIGHTH),
    # ドドシsシsソsソsソsソs むねのどあをたたいても

    #('E3', EIGHTH), ('Gs3', EIGHTH), ('B3',EIGHTH), ('E3', EIGHTH), ('A3', EIGHTH), ('B3',EIGHTH), ('C4', EIGHTH), ('A3', EIGHTH),

    #('Fs3', EIGHTH), ('A3', EIGHTH), ('D4', EIGHTH), ('D3', EIGHTH), ('G3', EIGHTH), ('D3',EIGHTH), ('G3', QUATER),

    #('E3', EIGHTH), ('Cs3', EIGHTH), ('A2',EIGHTH), ('A2', EIGHTH), ('D3', EIGHTH), ('E3',EIGHTH), ('F3', EIGHTH), ('D3', EIGHTH),

    #('G3', QUATER), ('G3', QUATER), ('A3', EIGHTH), ('G2', EIGHTH), ('A3', EIGHTH),

    #('G3', QUATER), ('A2', QUATER), ('D3', EIGHTH), ('C3', EIGHTH), ('B2', QUATER),

    #('F3', EIGHTH + SIXTEENTH), ('F3', EIGHTH + SIXTEENTH), ('E3', EIGHTH + SIXTEENTH), 
    #('D3', EIGHTH), ('F3', EIGHTH), ('A3', EIGHTH), ('C4', EIGHTH),
    #('G3', QUATER), ('G3', QUATER), ('C4', QUATER + QUATER)
#]

#backing_wave = np.hstack([generate_note(tone, value) for tone, value in backing_notes])
#Eva_back_audio = IPython.display.Audio(backing_wave, rate=BIT_RATE)
#IPython.display.display(Eva_back_audio)

#%%
#Eva_wave = np.vstack((melody_wave, backing_wave)) # メロディとバッキングを合わせる、音の長さが同じでないとエラー
#IPython.display.Audio(Eva_wave, rate=BIT_RATE)
