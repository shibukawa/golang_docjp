.. golang_docjp documentation master file, created by
   sphinx-quickstart on Fri Nov 13 01:12:54 2009.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

プログラミング言語 Go ドキュメント
==================================

目次:

.. toctree::
   :hidden:
   
   go_tutorial
   effective_go
   
   command_documentation
   package_documentation

チュートリアル
---------------

:ref:`go_tutorial`

Effective Go
------------

:ref:`effective_go`

Programming
---------------

:ref:`command_documentation`
:ref:`package_documentation`

索引とテーブル
==============

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

翻訳プロジェクトについて
========================

この翻訳は `Sphinx <http://sphinx.shibu.jp/>`_ を使って以下のサイトで共同で行っています。翻訳に参加ご希望の方は、 yoshiki at shibu.jp までご連絡ください。

http://bitbucket.org/shibu/golang-docjp/

必要なモノ
----------

* Mercurial(バージョン管理)
* Sphinx(ドキュメント作成)

翻訳ルール
----------

* まずはBitbucketのアカウントを作ってご連絡ください。書き込み権限を設定します。
* 翻訳したいものがある方は上記のBitbucketのサイト上でチケットを発行してください。Issueタブです。大項目単位です。
* 翻訳は読みやすさを考えて「です・ます」調で統一します。
* Sphinxディレクティブを使った相互リンクは暇なときに設定します。もしくは書き込み権限のある方は設定してもらっても構いません。
* Mercurialでcloneして翻訳してからcommit、pushしてください。commit前にpull&update(変更があったときだけ)を忘れずに。忘れるとコンフリクトします。コンフリクトした時の対処法など、詳しくは藤原さんのすばらしいMercurial本を参照してください。

翻訳プロジェクト参加者(登録順)
------------------------------

* SHIBUKAWA Yoshiki / shibu
* a o / kuma8
* Akira Kitada / akitada
* Kosei Kitahara / Surgo
* Shinji Kinoshita / snj
* ymotongpoo
* Hideo Hattori / hhatto
* Yoji TAKEUCHI / ytakeuch
* Takayuki Shimizukawa / shimizukawa
* Daigo Shitara / sdaigo
* murapong

ライセンス
----------

本家のサイトと同じく、Creative Commons 3.0にて公開します。オリジナルの著者は **The Go Authors** です。翻訳は pyspa.org のメンバーにより、行っています。